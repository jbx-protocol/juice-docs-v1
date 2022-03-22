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
  Records newly redeemed tokens of a project.

  @dev
  The msg.sender must be an IJBPaymentTerminal. The amount specified in the params is in terms of the msg.sender's tokens.

  @param _holder The account that is having its tokens redeemed.
  @param _projectId The ID of the project to which the tokens being redeemed belong.
  @param _tokenCount The number of project tokens to redeem, as a fixed point number with 18 decimals.
  @param _balanceDecimals The amount of decimals expected in the returned `reclaimAmount`.
  @param _balanceCurrency The currency that the stored balance is expected to be in terms of.
  @param _memo A memo to pass along to the emitted event.
  @param _metadata Bytes to send along to the data source, if one is provided.

  @return fundingCycle The funding cycle during which the redemption was made.
  @return reclaimAmount The amount of terminal tokens reclaimed, as a fixed point number with 18 decimals.
  @return delegate A delegate contract to use for subsequent calls.
  @return memo A memo that should be passed along to the emitted event.
*/
function recordRedemptionFor(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _balanceDecimals,
  uint256 _balanceCurrency,
  string calldata _memo,
  bytes calldata _metadata
)
  external
  override
  nonReentrant
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 reclaimAmount,
    IJBRedemptionDelegate delegate,
    string memory memo
  )
{
  // The holder must have the specified number of the project's tokens.
  if (tokenStore.balanceOf(_holder, _projectId) < _tokenCount) revert INSUFFICIENT_TOKENS();

  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // The current funding cycle must not be paused.
  if (fundingCycle.redeemPaused()) revert FUNDING_CYCLE_REDEEM_PAUSED();

  // If the funding cycle has configured a data source, use it to derive a claim amount and memo.
  if (fundingCycle.useDataSourceForRedeem()) {
    // Create the params that'll be sent to the data source.
    JBRedeemParamsData memory _data = JBRedeemParamsData(
      IJBPaymentTerminal(msg.sender),
      _holder,
      _tokenCount,
      _balanceDecimals,
      _projectId,
      fundingCycle.redemptionRate(),
      fundingCycle.ballotRedemptionRate(),
      _balanceCurrency,
      _memo,
      _metadata
    );
    (reclaimAmount, memo, delegate) = fundingCycle.dataSource().redeemParams(_data);
  } else {
    // Get the amount of current overflow.
    // Use the local overflow if the funding cycle specifies that it should be used. Otherwise use the project's total overflow across all of its terminals.
    uint256 _currentOverflow = fundingCycle.useTotalOverflowForRedemptions()
      ? _currentTotalOverflowOf(_projectId, _balanceDecimals, _balanceCurrency)
      : _overflowDuring(
        IJBPaymentTerminal(msg.sender),
        _projectId,
        fundingCycle,
        _balanceCurrency
      );

    // If there is no overflow, nothing is reclaimable.
    reclaimAmount = _currentOverflow == 0
      ? 0
      : _reclaimableOverflowDuring(_projectId, fundingCycle, _tokenCount, _currentOverflow);
    memo = _memo;
  }

  // The amount being reclaimed must be within the project's balance.
  if (reclaimAmount > balanceOf[IJBPaymentTerminal(msg.sender)][_projectId])
    revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();

  // Remove the reclaimed funds from the project's balance.
  if (reclaimAmount > 0)
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
      balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] -
      reclaimAmount;
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
