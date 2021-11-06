# redeemTokensOf

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../../../protocol/interfaces/ijbethterminalof.md)

{% tabs %}
{% tab title="Step by step" %}
**Addresses can redeem their tokens to claim the project's overflowed ETH, or to trigger rules determined by the project's current funding cycle's data source.**

_Only a token's holder or a designated operator can redeem it._

## Definition

```solidity
function redeemTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedWei,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  override
  nonReentrant
  requirePermission(_holder, _projectId, JBOperations.REDEEM)
  returns (uint256 claimAmount) { ... }
```

* Arguments:
  * `_holder` is the account to redeem tokens for.
  * `_projectId` is the ID of the project to which the tokens being redeemed belong.
  * `_tokenCount` is the number of tokens to redeem.
  * `_minReturnedWei` is the minimum amount of Wei expected in return.
  * `_beneficiary` is the address to send the ETH to. Send the address this contract to burn the count.
  * `_memo` is a memo to pass along to the emitted event.
  * `_delegateMetadata` are bytes to send along to the delegate, if one is provided.
* Through the [`requirePermission`](../../../../../../protocol/specifications/contracts/or-payment-terminals/or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.REDEEM` permission by the project owner for the provided `_projectId`.
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
* The function returns the amount of ETH that the tokens were redeemed for, in wei.

## Body

1.  Make sure the provided beneficiary of the claimed funds isn't the zero address.

    ```solidity
    // Can't send claimed funds to the zero address.
    require(_beneficiary != address(0), '0x4c: ZERO_ADDRESS');
    ```
2.  Define a reference to the project's funding cycle during which the redemption is being made.

    ```solidity
    // Keep a reference to the funding cycles during which the redemption is being made.
    JBFundingCycle memory _fundingCycle;
    ```
3.  Record the redemption in the store.

    ```solidity
    // Record the redemption.
    (_fundingCycle, claimAmount, _memo) = store.recordRedemptionFor(
      _holder,
      _projectId,
      _tokenCount,
      _minReturnedWei,
      _beneficiary,
      _memo,
      _delegateMetadata
    );
    ```

    _External references:_

    * [`recordRedemptionFor`](../../jbethpaymentterminalstore/write/recordredemptionfor.md)
4.  If an amount is being claimed, send the funds to the beneficiary.

    ```solidity
    // Send the claimed funds to the beneficiary.
    if (claimAmount > 0) Address.sendValue(_beneficiary, claimAmount);
    ```
5.  Emit a `Redeem` event with the relevant parameters.

    ```solidity
    emit Redeem(
      _fundingCycle.id,
      _projectId,
      _holder,
      _fundingCycle,
      _beneficiary,
      _tokenCount,
      claimAmount,
      _memo,
      msg.sender
    );
    ```

    _Event references:_

    * [`Redeem`](../../../../../../protocol/specifications/contracts/or-payment-terminals/jbethpaymentterminal/events/redeem.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Addresses can redeem their tokens to claim the project's overflowed ETH, or to trigger rules determined by the project's current funding cycle's data source.

  @dev
  Only a token's holder or a designated operator can redeem it.

  @param _holder The account to redeem tokens for.
  @param _projectId The ID of the project to which the tokens being redeemed belong.
  @param _tokenCount The number of tokens to redeem.
  @param _minReturnedWei The minimum amount of Wei expected in return.
  @param _beneficiary The address to send the ETH to. Send the address this contract to burn the count.
  @param _memo A memo to pass along to the emitted event.
  @param _delegateMetadata Bytes to send along to the delegate, if one is provided.

  @return claimAmount The amount of ETH that the tokens were redeemed for, in wei.
*/
function redeemTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedWei,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  override
  nonReentrant
  requirePermission(_holder, _projectId, JBOperations.REDEEM)
  returns (uint256 claimAmount)
{
  // Can't send claimed funds to the zero address.
  require(_beneficiary != address(0), '0x4c: ZERO_ADDRESS');

  // Keep a reference to the funding cycles during which the redemption is being made.
  JBFundingCycle memory _fundingCycle;

  // Record the redemption.
  (_fundingCycle, claimAmount, _memo) = store.recordRedemptionFor(
    _holder,
    _projectId,
    _tokenCount,
    _minReturnedWei,
    _beneficiary,
    _memo,
    _delegateMetadata
  );

  // Send the claimed funds to the beneficiary.
  if (claimAmount > 0) Address.sendValue(_beneficiary, claimAmount);

  emit Redeem(
    _fundingCycle.id,
    _projectId,
    _holder,
    _fundingCycle,
    _beneficiary,
    _tokenCount,
    claimAmount,
    _memo,
    msg.sender
  );
}
```
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
