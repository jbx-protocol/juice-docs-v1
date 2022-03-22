# recordRedemptionFor

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly redeemed tokens of a project.**

_Redeems the project's tokens according to values provided by a configured data source. If no data source is configured, redeems tokens along a redemption bonding curve that is a function of the number of tokens being burned._

_The msg.sender must be an [`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)._

#### Definition

```solidity
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
  ) { ... }
```

* Arguments:
  * `_holder` is the account that is having its tokens redeemed.
  * `_projectId` is the ID of the project to which the tokens being redeemed belong.
  * `_tokenCount` is the number of project tokens to redeem, as a fixed point number with 18 decimals.
  * `_balanceDecimals` is the amount of decimals expected in the returned `reclaimAmount`.
  * `_balanceCurrency` is the currency that the returned `reclaimAmount` is expected to be in terms of.
  * `_memo` is a memo to pass along to the emitted event.
  * `_metadata` are bytes to send along to the data source, if one is provided.
* The function returns:
  * `fundingCycle` is the funding cycle during which the redemption was made.
  * `reclaimAmount` is the amount of terminal tokens reclaimed, as a fixed point number with 18 decimals.
  * `delegate` is a delegate contract to use for subsequent calls.
  * `memo` is a memo that should be passed along to the emitted event.

#### Body

1.  Make sure the holder has at least as many tokens as is being redeemed.

    ```solidity
    // The holder must have the specified number of the project's tokens.
    if (tokenStore.balanceOf(_holder, _projectId) < _tokenCount) {
      revert INSUFFICIENT_TOKENS();
    }
    ```

    _External references:_

    * [`balanceOf`](../../../jbtokenstore/read/balanceof.md)
2.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
3.  Make sure the project's funding cycle isn't configured to pause redemptions.

    ```solidity
    // The current funding cycle must not be paused.
    if (fundingCycle.redeemPaused()) {
      revert FUNDING_CYCLE_REDEEM_PAUSED();
    }
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.redeemPaused(...)`
4.  Create a variable where a redemption delegate will be saved if there is one. This pay delegate will later have its method called if it exists.

    ```solidity
    // Save a reference to the delegate to use.
    IJBRedemptionDelegate _delegate;
    ```
5.  If the project's current funding cycle is configured to use a data source when making redemptions, ask the data source for the parameters that should be used throughout the rest of the function given provided contextual values in a [`JBRedeemParamsData`](../../../../data-structures/jbredeemparamsdata.md) structure. Otherwise default parameters are used.

    ```solidity
    // If the funding cycle has configured a data source, use it to derive a claim amount and memo.
    if (fundingCycle.useDataSourceForRedeem()) {
      (reclaimAmount, memo, _delegate, _delegateMetadata) = fundingCycle.dataSource().redeemParams(
        JBRedeemParamsData(
          _holder,
          _tokenCount,
          _projectId,
          fundingCycle.redemptionRate(),
          fundingCycle.ballotRedemptionRate(),
          _beneficiary,
          _memo,
          _delegateMetadata
        )
      );
    } else {
      reclaimAmount = _reclaimableOverflowOf(_projectId, fundingCycle, _tokenCount);
      memo = _memo;
    }
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.useDataSourceForRedeem(...)`\
      `.dataSource(...)`\
      `.redemptionRate(...)`\
      `.ballotRedemptionRate(...)`
6.  Make sure the amount being claimed is within the bounds of the project's balance.

    ```solidity
    // The amount being claimed must be within the project's balance.
    if (reclaimAmount > balanceOf[_projectId]) {
      revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
    }
    ```
7.  Make sure there is at least as much being claimed as expected.

    ```solidity
    // The amount being claimed must be at least as much as was expected.
    if (reclaimAmount < _minReturnedWei) {
      revert INADEQUATE_CLAIM_AMOUNT();
    }
    ```
8.  Burn tokens if needed.

    ```solidity
    // Redeem the tokens, which burns them.
    if (_tokenCount > 0)
      directory.controllerOf(_projectId).burnTokensOf(_holder, _projectId, _tokenCount, '', false);
    ```

    _External references:_

    * [`burnTokensOf`](../../../or-controllers/jbcontroller/write/burntokensof.md)
9.  Decrement any claimed funds from the project's balance if needed.

    ```solidity
    // Remove the redeemed funds from the project's balance.
    if (reclaimAmount > 0) balanceOf[_projectId] = balanceOf[_projectId] - reclaimAmount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
10. If a redemption delegate was provided by the data source, call its `didRedeem` function with a [`JBDidRedeemData`](../../../../data-structures/jbdidredeemdata.md) payload including contextual information. When finished, emit a `DelegateDidRedeem` event with the relevant parameters.

    ```solidity
    // If a delegate was returned by the data source, issue a callback to it.
    if (_delegate != IJBRedemptionDelegate(address(0))) {
      JBDidRedeemData memory _data = JBDidRedeemData(
        _holder,
        _projectId,
        _tokenCount,
        reclaimAmount,
        _beneficiary,
        memo,
        _delegateMetadata
      );
      _delegate.didRedeem(_data);
      emit DelegateDidRedeem(_delegate, _data);
    }
    ```

    _Event references:_

    * [`DelegateDidRedeem`](../events/delegatedidredeem.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly redeemed tokens of a project.

  @dev
  Redeems the project's tokens according to values provided by a configured data source. If no data source is configured, redeems tokens along a redemption bonding curve that is a function of the number of tokens being burned.

  @dev
  The msg.sender must be an IJBPaymentTerminal. 

  @param _holder The account that is having its tokens redeemed.
  @param _projectId The ID of the project to which the tokens being redeemed belong.
  @param _tokenCount The number of project tokens to redeem, as a fixed point number with 18 decimals.
  @param _balanceDecimals The amount of decimals expected in the returned `reclaimAmount`.
  @param _balanceCurrency The currency that the returned `reclaimAmount` is expected to be in terms of.
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
| String                                          | Description                                                                                  |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **`INSUFFICIENT_TOKENS`**                       | Thrown if holder doesn't have enough tokens in its balance to make the specified redemption. |
| **`FUNDING_CYCLE_REDEEM_PAUSED`**               | Thrown if the project has configured its current funding cycle to pause redemptions.         |
| **`INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE`** | Thrown if the project's balance isn't sufficient to fulfill the desired claim.               |
| **`INADEQUATE_CLAIM_AMOUNT`**                   | Thrown if the claim amount is less than the minimum expected.                                |
{% endtab %}

{% tab title="Events" %}
| Name                                                       | Data                                                                                                                                                                                                                                                      |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DelegateDidRedeem`**](../events/delegatedidredeem.md)) | <ul><li><a href="../../../../interfaces/ijbredemptiondelegate.md"><code>JBRedemptionDelegate</code></a><code>delegate</code></li><li><a href="../../../../data-structures/jbdidredeemdata.md"><code>JBDidRedeemData</code></a><code>data</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
