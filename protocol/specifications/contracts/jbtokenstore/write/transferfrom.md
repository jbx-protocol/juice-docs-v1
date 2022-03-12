# transferFrom

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a holder to transfer unclaimed tokens to another account.**

_Only a token holder or an operator can transfer its unclaimed tokens._

#### Definition

```solidity
function transferFrom(
  address _holder,
  uint256 _projectId,
  address _recipient,
  uint256 _amount
) external override requirePermission(_holder, _projectId, JBOperations.TRANSFER) { ... }
```

* Arguments:
  * `_holder` is the address to transfer tokens from.
  * `_projectId` is the ID of the project whose tokens are being transferred.
  * `_recipient` is thhe recipient of the tokens.
  * `_amount` is the amount of tokens to transfer.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the [`JBOperations.TRANSFER`](../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) interface.
* The function doesn't return anything.

#### Body

1.  Make sure a non-zero recipient was specified.

    ```solidity
    // Can't transfer to the zero address.
    if (_recipient == address(0)) revert RECIPIENT_ZERO_ADDRESS();
    ```
2.  Get a reference to the amount of unclaimed project tokens the holder has.

    ```solidity
    // Get a reference to the holder's unclaimed project token balance.
    uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
3.  Make sure the holder has enough unclaimed tokens to transfer.

    ```solidity
    // The holder must have enough unclaimed tokens to transfer.
    if (_amount > _unclaimedBalance) revert INSUFFICIENT_UNCLAIMED_TOKENS();
    ```
4.  Subtract the amount from the holder's unclaimed balance of project tokens. 

    ```solidity
    // Subtract from the holder's unclaimed token balance.
    unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
5.  Add the amount of unclaimed project tokens to the recipient's balance.

    ```solidity
    // Add the unclaimed project tokens to the recipient's balance.
    unclaimedBalanceOf[_recipient][_projectId] =
      unclaimedBalanceOf[_recipient][_projectId] +
      _amount;
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
6.  Emit a `Transfer` event with the relevant parameters.

    ```solidity
    emit Transfer(_holder, _projectId, _recipient, _amount, msg.sender);
    ```

    _Event references:_

    * [`Transfer`](../events/transfer.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Allows a holder to transfer unclaimed tokens to another account.

  @dev
  Only a token holder or an operator can transfer its unclaimed tokens.

  @param _holder The address to transfer tokens from.
  @param _projectId The ID of the project whose tokens are being transferred.
  @param _recipient The recipient of the tokens.
  @param _amount The amount of tokens to transfer.
*/
function transferFrom(
  address _holder,
  uint256 _projectId,
  address _recipient,
  uint256 _amount
) external override requirePermission(_holder, _projectId, JBOperations.TRANSFER) {
  // Can't transfer to the zero address.
  if (_recipient == address(0)) revert RECIPIENT_ZERO_ADDRESS();

  // Get a reference to the holder's unclaimed project token balance.
  uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];

  // The holder must have enough unclaimed tokens to transfer.
  if (_amount > _unclaimedBalance) revert INSUFFICIENT_UNCLAIMED_TOKENS();

  // Subtract from the holder's unclaimed token balance.
  unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;

  // Add the unclaimed project tokens to the recipient's balance.
  unclaimedBalanceOf[_recipient][_projectId] =
    unclaimedBalanceOf[_recipient][_projectId] +
    _amount;

  emit Transfer(_holder, _projectId, _recipient, _amount, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                              | Description                                                  |
| ----------------------------------- | ------------------------------------------------------------ |
| **`RECIPIENT_ZERO_ADDRESS`**        | Thrown if no recipient was speicified.                       |
| **`INSUFFICIENT_UNCLAIMED_TOKENS`** | Thrown if the holder doesn't have enough tokens to transfer. |
{% endtab %}

{% tab title="Events" %}
| Name                                    | Data                                                                                                                                                                                                                        |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Transfer`**](events/transfer.md)                     | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed recipient</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul>                                                   |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
