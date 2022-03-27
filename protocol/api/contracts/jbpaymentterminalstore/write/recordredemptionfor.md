# recordRedemptionFor

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)

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
  string memory _memo,
  bytes memory _metadata
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
* The resulting function overrides a function definition from the [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md) interface.
* The function returns:
  * `fundingCycle` is the funding cycle during which the redemption was made.
  * `reclaimAmount` is the amount of terminal tokens reclaimed, as a fixed point number with 18 decimals.
  * `delegate` is a delegate contract to use for subsequent calls.
  * `memo` is a memo that should be passed along to the emitted event.

#### Body

1.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../jbfundingcyclestore/read/currentof.md)
2.  Make sure the project's funding cycle isn't configured to pause redemptions.

    ```solidity
    // The current funding cycle must not be paused.
    if (fundingCycle.redeemPaused()) revert FUNDING_CYCLE_REDEEM_PAUSED();
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.redeemPaused(...)`
3.  The following scoped block is a bit of a hack to prevent a "Stack too deep" error. 

    ```solidity
    // Scoped section prevents stack too deep. `_currentOverflow`, `_totalSupply`, and `_data` only used within scope.
    { ... }
    ```

    1.  Get a reference to the amount of overflow the project has. Either the project's total overflow or the overflow local to the msg.sender's balance will be used depending on how the project's funding cycle is configured. 

        ```solidity
        // Get the amount of current overflow.
        // Use the local overflow if the funding cycle specifies that it should be used. Otherwise, use the project's total overflow across all of its terminals.
        uint256 _currentOverflow = fundingCycle.useTotalOverflowForRedemptions()
          ? _currentTotalOverflowOf(_projectId, _balanceDecimals, _balanceCurrency)
          : _overflowDuring(
            IJBPaymentTerminal(msg.sender),
            _projectId,
            fundingCycle,
            _balanceCurrency
          );
        ```

        _Libraries used:_

        * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)\
          `.useTotalOverflowForRedemptions(...)`\

        _Internal references:_

        * [`_currentTotalOverflowOf`](../read/_currenttotaloverflowof.md)
        * [`_overflowDuring`](../read/_overflowduring.md)

    2.  Get a reference to the total outstanding supply of project tokens.

        ```solidity
        // Get the number of outstanding tokens the project has.
        uint256 _totalSupply = directory.controllerOf(_projectId).totalOutstandingTokensOf(
          _projectId,
          fundingCycle.reservedRate()
        );
        ```

        _Libraries used:_

        * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)
          * `.reservedRate(...)`

        _Internal references:_

        * [`directory`](../properties/directory.md)

        _External references:_

        * [`controllerOf`](../../jbdirectory/properties/controllerof.md)
        * [`totalOutstandingTokensOf`](../../or-controllers/jbcontroller/read/totaloutstandingtokensof.md)

    3.  Get a reference to the reclaimable overflow if there is overflow. 

        ```solidity
        if (_currentOverflow > 0)
          // Calculate reclaim amount using the current overflow amount.
          reclaimAmount = _reclaimableOverflowDuring(
            _projectId,
            fundingCycle,
            _tokenCount,
            _totalSupply,
            _currentOverflow
          );
        ```

        _Internal references:_

        * [`_reclaimableOverflowDuring`](../read/_reclaimableoverflowduring.md)

4.  If the project's current funding cycle is configured to use a data source when making redemptions, ask the data source for the parameters that should be used throughout the rest of the function given provided contextual values in a [`JBRedeemParamsData`](../../../data-structures/jbredeemparamsdata.md) structure. Otherwise default parameters are used.

    ```solidity
    // If the funding cycle has configured a data source, use it to derive a claim amount and memo.
    if (fundingCycle.useDataSourceForRedeem()) {
      // Create the params that'll be sent to the data source.
      JBRedeemParamsData memory _data = JBRedeemParamsData(
        IJBPaymentTerminal(msg.sender),
        _holder,
        _projectId,
        _tokenCount,
        _totalSupply,
        _currentOverflow,
        _balanceDecimals,
        _balanceCurrency,
        reclaimAmount,
        fundingCycle.useTotalOverflowForRedemptions(),
        fundingCycle.redemptionRate(),
        fundingCycle.ballotRedemptionRate(),
        _memo,
        _metadata
      );
      (reclaimAmount, memo, delegate) = fundingCycle.dataSource().redeemParams(_data);
    } else {
      memo = _memo;
    }
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.useDataSourceForRedeem(...)`\
      `.dataSource(...)`\
      `.redemptionRate(...)`\
      `.ballotRedemptionRate(...)`\
      `.useTotalOverflowForRedemptions(...)`

5.  Make sure the amount being claimed is within the bounds of the project's balance.

    ```solidity
    // The amount being reclaimed must be within the project's balance.
    if (reclaimAmount > balanceOf[IJBPaymentTerminal(msg.sender)][_projectId])
      revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
6.  Decrement any claimed funds from the project's balance if needed.

    ```solidity
    // Remove the reclaimed funds from the project's balance.
    if (reclaimAmount > 0)
      balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
        balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] -
        reclaimAmount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
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
  string memory _memo,
  bytes memory _metadata
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
  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // The current funding cycle must not be paused.
  if (fundingCycle.redeemPaused()) revert FUNDING_CYCLE_REDEEM_PAUSED();

  // Scoped section prevents stack too deep. `_currentOverflow`, `_totalSupply`, and `_data` only used within scope.
  {
    // Get the amount of current overflow.
    // Use the local overflow if the funding cycle specifies that it should be used. Otherwise, use the project's total overflow across all of its terminals.
    uint256 _currentOverflow = fundingCycle.useTotalOverflowForRedemptions()
      ? _currentTotalOverflowOf(_projectId, _balanceDecimals, _balanceCurrency)
      : _overflowDuring(
        IJBPaymentTerminal(msg.sender),
        _projectId,
        fundingCycle,
        _balanceCurrency
      );

    // Get the number of outstanding tokens the project has.
    uint256 _totalSupply = directory.controllerOf(_projectId).totalOutstandingTokensOf(
      _projectId,
      fundingCycle.reservedRate()
    );

    if (_currentOverflow > 0)
      // Calculate reclaim amount using the current overflow amount.
      reclaimAmount = _reclaimableOverflowDuring(
        _projectId,
        fundingCycle,
        _tokenCount,
        _totalSupply,
        _currentOverflow
      );

    // If the funding cycle has configured a data source, use it to derive a claim amount and memo.
    if (fundingCycle.useDataSourceForRedeem()) {
      // Create the params that'll be sent to the data source.
      JBRedeemParamsData memory _data = JBRedeemParamsData(
        IJBPaymentTerminal(msg.sender),
        _holder,
        _projectId,
        _tokenCount,
        _totalSupply,
        _currentOverflow,
        _balanceDecimals,
        _balanceCurrency,
        reclaimAmount,
        fundingCycle.useTotalOverflowForRedemptions(),
        fundingCycle.redemptionRate(),
        fundingCycle.ballotRedemptionRate(),
        _memo,
        _metadata
      );
      (reclaimAmount, memo, delegate) = fundingCycle.dataSource().redeemParams(_data);
    } else {
      memo = _memo;
    }
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
| **`FUNDING_CYCLE_REDEEM_PAUSED`**               | Thrown if the project has configured its current funding cycle to pause redemptions.         |
| **`INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE`** | Thrown if the project's balance isn't sufficient to fulfill the desired claim.               |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
