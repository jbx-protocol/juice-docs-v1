# recordPaymentFrom

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly contributed tokens to a project.**

_Mint's the project's tokens according to values provided by a configured data source. If no data source is configured, mints tokens proportional to the amount of the contribution._

_The msg.sender must be an [`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md). The amount specified in the params is in terms of the msg.senders tokens._

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

1.  Get a reference to the project's current funding cycle that should be returned.

    ```solidity
    // Get a reference to the current funding cycle for the project.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
2.  Make sure the project has a funding cycle configured. This is done by checking if the project's current funding cycle is non-zero.

    ```solidity
    // The project must have a funding cycle configured.
    if (fundingCycle.number == 0) {
      revert INVALID_FUNDING_CYCLE();
    }
    ```
3.  Make sure the project's funding cycle isn't configured to pause payments.

    ```solidity
    // Must not be paused.
    if (fundingCycle.payPaused()) {
      revert FUNDING_CYCLE_PAYMENT_PAUSED();
    }
    ```
4.  Create a variable where a pay delegate will be saved if there is one. This pay delegate will later have it's method called if it exists.

    ```solidity
    // Save a reference to the delegate to use.
    IJBPayDelegate _delegate;
    ```
5.  If the project's current funding cycle is configured to use a data source when receiving payments, ask the data source for the parameters that should be used throughout the rest of the function given provided contextual values in a [`JBPayParamsData`](../../../../data-structures/jbpayparamsdata.md) structure. Otherwise default parameters are used.

    ```solidity
    // If the funding cycle has configured a data source, use it to derive a weight and memo.
    if (fundingCycle.useDataSourceForPay()) {
      (weight, memo, _delegate, _delegateMetadata) = fundingCycle.dataSource().payParams(
        JBPayParamsData(
          _payer,
          _amount,
          _projectId,
          fundingCycle.weight,
          fundingCycle.reservedRate(),
          address(uint160(_preferClaimedTokensAndBeneficiary >> 1)),
          _memo,
          _delegateMetadata
        )
      );
      // Otherwise use the funding cycle's weight
    } else {
      weight = fundingCycle.weight;
      memo = _memo;
    }
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.useDataSourceForPay(...)`\
      `.dataSource(...)`\
      `.reservedRate(...)`
6.  Calculate the weighted amount, which is the payment amount multiplied by the appropriate weight.

    ```solidity
    // Multiply the amount by the weight to determine the amount of tokens to mint.
    uint256 _weightedAmount = PRBMathUD60x18.mul(_amount, weight);
    ```

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.mul` 7. Increment the project's balance by the amount of the payment received.

    ```solidity
    // Add the amount to the ETH balance of the project if needed.
    if (_amount > 0) balanceOf[_projectId] = balanceOf[_projectId] + _amount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
7.  If a there is a weighted amount, mint tokens accordingly. Unpack the `_preferClaimedTokensAndBeneficiary` value into its parts to pass along.

    ```solidity
    if (_weightedAmount > 0)
      directory.controllerOf(_projectId).mintTokensOf(
        _projectId,
        _weightedAmount,
        address(uint160(_preferClaimedTokensAndBeneficiary >> 1)),
        '',
        (_preferClaimedTokensAndBeneficiary & 1) == 1,
        fundingCycle.reservedRate()
      );
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.reservedRate(...)`

    _External references:_

    * [`mintTokensOf`](../../../or-controllers/jbcontroller/write/minttokensof.md)
8.  Make sure there were at least as many tokens minted as expected.

    ```solidity
    // The token count for the beneficiary must be greater than or equal to the minimum expected.
    if (tokenCount < _minReturnedTokens) {
      revert INADEQUATE_TOKEN_COUNT();
    }
    ```
9.  If a pay delegate was provided by the data source, call its `didPay` function with a [`JBDidPayData`](../../../../data-structures/jbdidpaydata.md) payload including contextual information. When finished, emit a `DelegateDidPay` event with the relevant parameters.

    ```solidity
    // If a delegate was returned by the data source, issue a callback to it.
    if (_delegate != IJBPayDelegate(address(0))) {
      JBDidPayData memory _data = JBDidPayData(
        _payer,
        _projectId,
        _amount,
        weight,
        tokenCount,
        payable(address(uint160(_preferClaimedTokensAndBeneficiary >> 1))),
        memo,
        _delegateMetadata
      );
      _delegate.didPay(_data);
      emit DelegateDidPay(_delegate, _data);
    }
    ```

    _Event references:_

    * [`DelegateDidPay`](../events/delegatedidpay.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly contributed tokens to a project.

  @dev
  Mint's the project's tokens according to values provided by a configured data source. If no data source is configured, mints tokens proportional to the amount of the contribution.

  @dev
  The msg.sender must be an IJBPaymentTerminal. The amount specified in the params is in terms of the msg.senders tokens.

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

  // Add the amount to the token balance of the project if needed.
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
| **`INADEQUATE_TOKEN_COUNT`**       | Thrown if the quantity of tokens minted for the beneficiary is less than the minimum expected. |
{% endtab %}

{% tab title="Events" %}
| Name                                                | Data                                                                                                                                                                                                                                   |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DelegateDidPay`**](../events/delegatedidpay.md) | <ul><li><a href="../../../../interfaces/ijbpaydelegate.md"><code>IJBPayDelegate</code></a><code>delegate</code></li><li><a href="../../../../data-structures/jbdidpaydata.md"><code>JBDidPayData</code></a><code>data</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
