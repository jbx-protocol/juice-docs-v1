# recordPaymentFrom

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly contributed tokens to a project.**

_Mint's the project's tokens according to values provided by a configured data source. If no data source is configured, mints tokens proportional to the amount of the contribution._

_The msg.sender must be an [`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md). The amount specified in the params is in terms of the msg.sender's tokens._

#### Definition

```solidity
function recordPaymentFrom(
  address _payer,
  JBTokenAmount calldata _amount,
  uint256 _projectId,
  uint256 _baseWeightCurrency,
  string calldata _memo,
  bytes memory _metadata
)
  external
  override
  nonReentrant
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 tokenCount,
    IJBPayDelegate delegate,
    string memory memo
  ) { ... }
```

* Arguments:
  * `_payer` is the original address that sent the payment to the terminal.
  * `_amount` is a [`JBTokenAmount`](../../../interfaces/jbtokenamount.md) data structure specifying the amount of tokens being paid. Includes the token being paid, the value, the number of decimals included, and the currency of the amount.
  * `_projectId` is the ID of the project being paid.
  * `_baseWeightCurrency` is the currency to base token issuance on.
  * `_memo` is a memo to pass along to the emitted event, and passed along to the funding cycle's data source.
  * `_metadata` are bytes to send along to the data source, if one is provided.
* The function returns:
  * `fundingCycle` is the project's funding cycle during which payment was made.
  * `tokenCount` is the number of project tokens that were minted, as a fixed point number with 18 decimals.
  * `delegate` is a delegate contract to use for subsequent calls.
  * `memo` is a memo that should be passed along to the emitted event.

#### Body

1.  Get a reference to the project's current funding cycle that should have its properties used in the subsequent calculations and returned.

    ```solidity
    // Get a reference to the current funding cycle for the project.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
2.  Make sure the project has a funding cycle configured. This is done by checking if the project's current funding cycle number is non-zero.

    ```solidity
    // The project must have a funding cycle configured.
    if (fundingCycle.number == 0) revert INVALID_FUNDING_CYCLE();
    ```
3.  Make sure the project's funding cycle isn't configured to pause payments.

    ```solidity
    // Must not be paused.
    if (fundingCycle.payPaused()) revert FUNDING_CYCLE_PAYMENT_PAUSED();
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.payPaused(...)`
4.  Create a variable where the weight to use in subsquent calculations will be saved.

    ```solidity
    // The weight according to which new token supply is to be minted, as a fixed point number with 18 decimals.
    uint256 _weight;
    ```
5.  If the project's current funding cycle is configured to use a data source when receiving payments, ask the data source for the parameters that should be used throughout the rest of the function given provided contextual values in a [`JBPayParamsData`](../../../../data-structures/jbpayparamsdata.md) structure. Otherwise default parameters are used.

    ```solidity
    // If the funding cycle has configured a data source, use it to derive a weight and memo.
    if (fundingCycle.useDataSourceForPay()) {
      // Create the params that'll be sent to the data source.
      JBPayParamsData memory _data = JBPayParamsData(
        IJBPaymentTerminal(msg.sender),
        _payer,
        _amount,
        _projectId,
        fundingCycle.weight,
        fundingCycle.reservedRate(),
        _memo,
        _metadata
      );
      (_weight, memo, delegate) = fundingCycle.dataSource().payParams(_data);
    }
    // Otherwise use the funding cycle's weight
    else {
      _weight = fundingCycle.weight;
      memo = _memo;
    }
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.useDataSourceForPay(...)`\
      `.dataSource(...)`\
      `.reservedRate(...)`

    _External references:_

    * [`payParams`](../../../interfaces/ijbfundingcycledatasource.md)
6.  If there is no amount being recorded, there's nothing left to do so the current values can be returned.

    ```solidity
    // If there's no amount being recorded, there's nothing left to do.
    if (_amount.value == 0) return (fundingCycle, 0, delegate, memo);
    ```
7.  Add the amount being paid to the stored balance.

    ```solidity
    // Add the amount to the token balance of the project.
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
      balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] +
      _amount.value;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
8.  If there is no weight, the resulting token count will be 0. There's nothing left to do so the current values can be returned.

    ```solidity
    // If there's no weight, token count must be 0 so there's nothing left to do.
    if (_weight == 0) return (fundingCycle, 0, delegate, memo);
    ```
6.  Calculate the weight ratio. This allows a project to get paid in a certain token, but issue project tokens relative to a different base currency. The weight ratio will be used to divide the product of the paid amount and the weight to determine the number of tokens that should be distributed. Since the number of distributed tokens should be a fixed point number with 18 decimals, the weight ratio must have the same number of decimals as the amount to cancel it out and leave only the fidelity of the 18 decimal fixed point weight.

    ```solidity
    // Get a reference to the number of decimals in the amount. (prevents stack too deep).
    uint256 _decimals = _amount.decimals;

    // If the terminal should base its weight on a different currency from the terminal's currency, determine the factor.
    // The weight is always a fixed point mumber with 18 decimals. To ensure this, the ratio should use the same number of decimals as the `_amount`.
    uint256 _weightRatio = _amount.currency == _baseWeightCurrency
      ? 10**_decimals
      : prices.priceFor(_amount.currency, _baseWeightCurrency, _decimals);
    ```

    _External references:_

    * [`priceFor`](../../../contracts/jbprices/read/pricefor.md)

7.  Determine the number of tokens to mint.

    ```solidity
    // Find the number of tokens to mint, as a fixed point number with as many decimals as `weight` has.
    tokenCount = PRBMath.mulDiv(_amount.value, _weight, _weightRatio);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly contributed tokens to a project.

  @dev
  Mint's the project's tokens according to values provided by a configured data source. If no data source is configured, mints tokens proportional to the amount of the contribution.

  @dev
  The msg.sender must be an IJBPaymentTerminal. The amount specified in the params is in terms of the msg.sender's tokens.

  @param _payer The original address that sent the payment to the terminal.
  @param _amount The amount of tokens being paid. Includes the token being paid, the value, the number of decimals included, and the currency of the amount.
  @param _projectId The ID of the project being paid.
  @param _baseWeightCurrency The currency to base token issuance on.
  @param _memo A memo to pass along to the emitted event, and passed along to the funding cycle's data source.
  @param _metadata Bytes to send along to the data source, if one is provided.

  @return fundingCycle The project's funding cycle during which payment was made.
  @return tokenCount The number of project tokens that were minted, as a fixed point number with 18 decimals.
  @return delegate A delegate contract to use for subsequent calls.
  @return memo A memo that should be passed along to the emitted event.
*/
function recordPaymentFrom(
  address _payer,
  JBTokenAmount calldata _amount,
  uint256 _projectId,
  uint256 _baseWeightCurrency,
  string calldata _memo,
  bytes calldata _metadata
)
  external
  override
  nonReentrant
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 tokenCount,
    IJBPayDelegate delegate,
    string memory memo
  )
{
  // Get a reference to the current funding cycle for the project.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // The project must have a funding cycle configured.
  if (fundingCycle.number == 0) revert INVALID_FUNDING_CYCLE();

  // Must not be paused.
  if (fundingCycle.payPaused()) revert FUNDING_CYCLE_PAYMENT_PAUSED();

  // The weight according to which new token supply is to be minted, as a fixed point number with 18 decimals.
  uint256 _weight;

  // If the funding cycle has configured a data source, use it to derive a weight and memo.
  if (fundingCycle.useDataSourceForPay()) {
    // Create the params that'll be sent to the data source.
    JBPayParamsData memory _data = JBPayParamsData(
      IJBPaymentTerminal(msg.sender),
      _payer,
      _amount,
      _projectId,
      fundingCycle.weight,
      fundingCycle.reservedRate(),
      _memo,
      _metadata
    );
    (_weight, memo, delegate) = fundingCycle.dataSource().payParams(_data);
  }
  // Otherwise use the funding cycle's weight
  else {
    _weight = fundingCycle.weight;
    memo = _memo;
  }

  // If there's no amount being recorded, there's nothing left to do.
  if (_amount.value == 0) return (fundingCycle, 0, delegate, memo);

  // Add the amount to the token balance of the project.
  balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] +
    _amount.value;

  // If there's no weight, token count must be 0 so there's nothing left to do.
  if (_weight == 0) return (fundingCycle, 0, delegate, memo);

  // Get a reference to the number of decimals in the amount. (prevents stack too deep).
  uint256 _decimals = _amount.decimals;

  // If the terminal should base its weight on a different currency from the terminal's currency, determine the factor.
  // The weight is always a fixed point mumber with 18 decimals. To ensure this, the ratio should use the same number of decimals as the `_amount`.
  uint256 _weightRatio = _amount.currency == _baseWeightCurrency
    ? 10**_decimals
    : prices.priceFor(_amount.currency, _baseWeightCurrency, _decimals);

  // Find the number of tokens to mint, as a fixed point number with as many decimals as `weight` has.
  tokenCount = PRBMath.mulDiv(_amount.value, _weight, _weightRatio);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                             | Description                                                                                    |
| ---------------------------------- | ---------------------------------------------------------------------------------------------- |
| **`INVALID_FUNDING_CYCLE`**        | Thrown if the project doesn't have a funding cycle.                                            |
| **`FUNDING_CYCLE_PAYMENT_PAUSED`** | Thrown if the project has configured its current funding cycle to pause payments.              |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
