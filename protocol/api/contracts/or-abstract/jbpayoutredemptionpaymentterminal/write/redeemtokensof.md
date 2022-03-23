# redeemTokensOf

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Holders can redeem their tokens to claim the project's overflowed tokens, or to trigger rules determined by the project's current funding cycle's data source.**

_Only a token holder or a designated operator can redeem it's tokens._

#### Definition

```solidity
function redeemTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedTokens,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _metadata
)
  external
  virtual
  override
  requirePermission(_holder, _projectId, JBOperations.REDEEM)
  returns (uint256 reclaimAmount) { ... }
```

* Arguments:
  * `_holder` is the account to redeem tokens for.
  * `_projectId` is the ID of the project to which the tokens being redeemed belong.
  * `_tokenCount` is the number of project tokens to redeem, as a fixed point number with 18 decimals.
  * `_minReturnedTokens` is the minimum amount of terminal tokens expected in return, as a fixed point number with the same amount of decimals as this terminal.
  * `_beneficiary` is the address to send the terminal tokens to.
  * `_memo` is a memo to pass along to the emitted event.
  * `_metadata` are bytes to send along to the data source and delegate, if provided.
* Through the [`requirePermission`](../../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the token holder, or from an operator that has been given the [`JBOperations.REDEEM`](../../../libraries/jboperations.md) permission by the token holder.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
* The function returns the amount of terminal tokens that the tokens were redeemed for, as a fixed point number with the same amount of decimals as this terminal.

#### Body

1.  Make sure the provided beneficiary of the claimed funds isn't the zero address.

    ```solidity
    // Can't send reclaimed funds to the zero address.
    if (_beneficiary == address(0)) revert REDEEM_TO_ZERO_ADDRESS();
    ```
2.  Define a reference to the project's funding cycle during which the redemption is being made.

    ```solidity
    // Keep a reference to the funding cycle during which the redemption is being made.
    JBFundingCycle memory _fundingCycle;
    ```
3.  The following scoped block is a bit of a hack to prevent a "Stack too deep" error. 

    ```solidity
    // Scoped section prevents stack too deep. `_delegate` only used within scope.
    { ... }
    ```

    1.  Get a reference to the redemption delegate that. 

        ```solidity
        IJBRedemptionDelegate _delegate;
        ```

    2.  Record the redemption and get a reference to the funding cycle during which the redemption was made, the terminal token amount that should be reclaimed, a delegate to callback to, and an updated memo.

        ```solidity
        // Record the redemption.
        (_fundingCycle, reclaimAmount, _delegate, _memo) = store.recordRedemptionFor(
          _holder,
          _projectId,
          _tokenCount,
          decimals, // The fixed point balance has this terminal's token's number of decimals.
          currency, // The balance is in terms of this terminal's currency.
          _memo,
          _metadata
        );
        ```

        _External references:_

        * [`recordRedemptionFor`](../../../jbpaymentterminalstore/write/recordredemptionfor.md)

    3.  Make sure the amount of terminal tokens being reclaimed is at least as much as the specified minimum.

        ```solidity
        // The amount being reclaimed must be at least as much as was expected.
        if (reclaimAmount < _minReturnedTokens) revert INADEQUATE_RECLAIM_AMOUNT();
        ```

    4.  Burn the project's tokens if needed.

        ```solidity
        // Burn the project tokens.
        if (_tokenCount > 0)
          directory.controllerOf(_projectId).burnTokensOf(
            _holder,
            _projectId,
            _tokenCount,
            '',
            false
          );
        ```

        _External references:_

        * [`burnTokensOf`](../../../or-controllers/jbcontroller/write/burntokensof.md)

    5.  If a delegate was provided, callback to its `didRedeem` function, and emit an event.

        ```solidity
        // If a delegate was returned by the data source, issue a callback to it.
        if (_delegate != IJBRedemptionDelegate(address(0))) {
          JBDidRedeemData memory _data = JBDidRedeemData(
            _holder,
            _projectId,
            _tokenCount,
            JBTokenAmount(token, reclaimAmount, decimals, currency),
            _beneficiary,
            _memo,
            _metadata
          );
          _delegate.didRedeem(_data);
          emit DelegateDidRedeem(_delegate, _data, msg.sender);
        }
        ```

        _External references:_

        * [`didRedeem`](../../../../interfaces/ijbredemptiondelegate.md)

        _Event references:_

        * [`DelegateDidRedeem`](../events/delegatedidredeem.md)

4.  If an amount is being reclaimed, send the funds to the beneficiary.

    ```solidity
    // Send the reclaimed funds to the beneficiary.
    if (reclaimAmount > 0) _transferFrom(address(this), _beneficiary, reclaimAmount);
    ```

5.  Emit a `RedeemTokens` event with the relevant parameters.

    ```solidity
    emit RedeemTokens(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _holder,
      _beneficiary,
      _tokenCount,
      reclaimAmount,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`RedeemTokens`](../events/redeemtokens.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Holders can redeem their tokens to claim the project's overflowed tokens, or to trigger rules determined by the project's current funding cycle's data source.

  @dev
  Only a token holder or a designated operator can redeem it's tokens.

  @param _holder The account to redeem tokens for.
  @param _projectId The ID of the project to which the tokens being redeemed belong.
  @param _tokenCount The number of project tokens to redeem, as a fixed point number with 18 decimals.
  @param _minReturnedTokens The minimum amount of terminal tokens expected in return, as a fixed point number with the same amount of decimals as this terminal.
  @param _beneficiary The address to send the terminal tokens to.
  @param _memo A memo to pass along to the emitted event.
  @param _metadata Bytes to send along to the data source and delegate, if provided.

  @return reclaimAmount The amount of terminal tokens that the project tokens were redeemed for, as a fixed point number with 18 decimals.
*/
function redeemTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedTokens,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _metadata
)
  external
  virtual
  override
  requirePermission(_holder, _projectId, JBOperations.REDEEM)
  returns (uint256 reclaimAmount)
{
  // Can't send reclaimed funds to the zero address.
  if (_beneficiary == address(0)) revert REDEEM_TO_ZERO_ADDRESS();

  // Keep a reference to the funding cycle during which the redemption is being made.
  JBFundingCycle memory _fundingCycle;

  // Scoped section prevents stack too deep. `_delegate` only used within scope.
  {
    IJBRedemptionDelegate _delegate;

    // Record the redemption.
    (_fundingCycle, reclaimAmount, _delegate, _memo) = store.recordRedemptionFor(
      _holder,
      _projectId,
      _tokenCount,
      decimals, // The fixed point balance has this terminal's token's number of decimals.
      currency, // The balance is in terms of this terminal's currency.
      _memo,
      _metadata
    );

    // The amount being reclaimed must be at least as much as was expected.
    if (reclaimAmount < _minReturnedTokens) revert INADEQUATE_RECLAIM_AMOUNT();

    // Burn the project tokens.
    if (_tokenCount > 0)
      directory.controllerOf(_projectId).burnTokensOf(
        _holder,
        _projectId,
        _tokenCount,
        '',
        false
      );

    // If a delegate was returned by the data source, issue a callback to it.
    if (_delegate != IJBRedemptionDelegate(address(0))) {
      JBDidRedeemData memory _data = JBDidRedeemData(
        _holder,
        _projectId,
        _tokenCount,
        JBTokenAmount(token, reclaimAmount, decimals, currency),
        _beneficiary,
        _memo,
        _metadata
      );
      _delegate.didRedeem(_data);
      emit DelegateDidRedeem(_delegate, _data, msg.sender);
    }
  }

  // Send the reclaimed funds to the beneficiary.
  if (reclaimAmount > 0) _transferFrom(address(this), _beneficiary, reclaimAmount);

  emit RedeemTokens(
    _fundingCycle.configuration,
    _fundingCycle.number,
    _projectId,
    _holder,
    _beneficiary,
    _tokenCount,
    reclaimAmount,
    _memo,
    msg.sender
  );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                       | Description                                             |
| ---------------------------- | ------------------------------------------------------- |
| **`REDEEM_TO_ZERO_ADDRESS`** | Thrown if the zero address was sent as the beneficiary. |
| **`INADEQUATE_RECLAIM_AMOUNT`** | Thrown if the amount being reclaimed is less than the specified minimum. |
{% endtab %}

{% tab title="Events" %}
| Name                                           | Data                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`RedeemTokens`**](../events/processfees.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed holder</code></li><li><a href="../../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>address beneficiary</code></li><li><code>uint256 tokenCount</code></li><li><code>uint256 claimedAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
