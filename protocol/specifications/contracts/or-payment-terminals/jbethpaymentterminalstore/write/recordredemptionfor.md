# recordRedemptionFor

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly redeemed tokens of a project.**

_Only the associated payment terminal can record a redemption._

## Definition

```solidity
function recordRedemptionFor(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedWei,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  onlyAssociatedPaymentTerminal
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 claimAmount,
    string memory memo
  ) { ... }
```

* Arguments:
  * `_holder` is thhe account that is having its tokens redeemed.
  * `_projectId` is the ID of the project to which the tokens being redeemed belong.
  * `_tokenCount` is the number of tokens being redeemed.
  * `_minReturnedWei` is the minimum amount of wei expected in return.
  * `_beneficiary` is the address that will benefit from the claimed amount.
  * `_memo` is a memo to pass along to the emitted event.
  * `_delegateMetadata` are bytes to send along to the delegate, if one is used.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns:
  * `fundingCycle` is the funding cycle during which the redemption was made.
  * `claimAmount` is the amount of wei claimed.
  * `memo` is a memo that should be included in the published event.

## Body

1.  Make sure the holder has at least as many tokens as is being redeemed.

    ```solidity
    // The holder must have the specified number of the project's tokens.
    require(tokenStore.balanceOf(_holder, _projectId) >= _tokenCount, '0x46: INSUFFICIENT_TOKENS');
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
    require(!fundingCycle.redeemPaused(), '0x47: PAUSED');
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
      (claimAmount, memo, _delegate, _delegateMetadata) = fundingCycle.dataSource().redeemParams(
        JBRedeemParamsData(
          _holder,
          _tokenCount,
          fundingCycle.redemptionRate(),
          fundingCycle.ballotRedemptionRate(),
          _beneficiary,
          _memo,
          _delegateMetadata
        )
      );
    } else {
      claimAmount = _claimableOverflowOf(_projectId, fundingCycle, _tokenCount);
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
    require(claimAmount <= balanceOf[_projectId], '0x48: INSUFFICIENT_FUNDS');
    ```
7.  Make sure there is at least as much being claimed as expected.

    ```solidity
    // The amount being claimed must be at least as much as was expected.
    require(claimAmount >= _minReturnedWei, '0x49: INADEQUATE');
    ```
8.  Burn tokens if needed.

    ```solidity
    // Redeem the tokens, which burns them.
    if (_tokenCount > 0)
      directory.controllerOf(_projectId).burnTokensOf(
        _holder,
        _projectId,
        _tokenCount,
        'Redeem for ETH',
        true
      );
    ```

    _External references:_

    * [`burnTokensOf`](../../../or-controllers/jbcontroller/write/burntokensof.md)
9.  Decrement any claimed funds from the project's balance if needed.

    ```solidity
    // Remove the redeemed funds from the project's balance.
    if (claimAmount > 0) balanceOf[_projectId] = balanceOf[_projectId] - claimAmount;
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
        claimAmount,
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
  Only the associated payment terminal can record a redemption.

  @param _holder The account that is having its tokens redeemed.
  @param _projectId The ID of the project to which the tokens being redeemed belong.
  @param _tokenCount The number of tokens being redeemed.
  @param _minReturnedWei The minimum amount of wei expected in return.
  @param _beneficiary The address that will benefit from the claimed amount.
  @param _memo A memo to pass along to the emitted event.
  @param _delegateMetadata Bytes to send along to the delegate, if one is used.

  @return fundingCycle The funding cycle during which the redemption was made.
  @return claimAmount The amount of wei claimed.
  @return memo A memo that should be passed along to the emitted event.
*/
function recordRedemptionFor(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedWei,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  onlyAssociatedPaymentTerminal
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 claimAmount,
    string memory memo
  )
{
  // The holder must have the specified number of the project's tokens.
  require(tokenStore.balanceOf(_holder, _projectId) >= _tokenCount, '0x46: INSUFFICIENT_TOKENS');

  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // The current funding cycle must not be paused.
  require(!fundingCycle.redeemPaused(), '0x47: PAUSED');

  // Save a reference to the delegate to use.
  IJBRedemptionDelegate _delegate;

  // If the funding cycle has configured a data source, use it to derive a claim amount and memo.
  if (fundingCycle.useDataSourceForRedeem()) {
    (claimAmount, memo, _delegate, _delegateMetadata) = fundingCycle.dataSource().redeemParams(
      JBRedeemParamsData(
        _holder,
        _tokenCount,
        fundingCycle.redemptionRate(),
        fundingCycle.ballotRedemptionRate(),
        _beneficiary,
        _memo,
        _delegateMetadata
      )
    );
  } else {
    claimAmount = _claimableOverflowOf(_projectId, fundingCycle, _tokenCount);
    memo = _memo;
  }

  // The amount being claimed must be within the project's balance.
  require(claimAmount <= balanceOf[_projectId], '0x48: INSUFFICIENT_FUNDS');

  // The amount being claimed must be at least as much as was expected.
  require(claimAmount >= _minReturnedWei, '0x49: INADEQUATE');

  // Redeem the tokens, which burns them.
  if (_tokenCount > 0)
    directory.controllerOf(_projectId).burnTokensOf(
      _holder,
      _projectId,
      _tokenCount,
      'Redeem for ETH',
      true
    );

  // Remove the redeemed funds from the project's balance.
  if (claimAmount > 0) balanceOf[_projectId] = balanceOf[_projectId] - claimAmount;

  // If a delegate was returned by the data source, issue a callback to it.
  if (_delegate != IJBRedemptionDelegate(address(0))) {
    JBDidRedeemData memory _data = JBDidRedeemData(
      _holder,
      _projectId,
      _tokenCount,
      claimAmount,
      _beneficiary,
      memo,
      _delegateMetadata
    );
    _delegate.didRedeem(_data);
    emit DelegateDidRedeem(_delegate, _data);
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                                                  |
| ------------------------------- | -------------------------------------------------------------------------------------------- |
| **`0x46: INSUFFICIENT_TOKENS`** | Thrown if holder doesn't have enough tokens in its balance to make the specified redemption. |
| **`0x47: PAUSED`**              | Thrown if the project has configured its current funding cycle to pause redemptions.         |
| **`0x48: INSUFFICIENT_FUNDS`**  | Thrown if the project's balance isn't sufficient to fulfill the desired claim.               |
| **`0x49: INADEQUATE`**          | Thrown if the claim amount is less than the minimum expected.                                |
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
