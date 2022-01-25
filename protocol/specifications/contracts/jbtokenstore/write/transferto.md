# transferTo

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows an unclaimed token holder to transfer them to another account, without claiming them to ERC-20s.**

_Only a token holder or an operator can transfer its unclaimed tokens_.

## Definition

```solidity
function transferTo(
  address _recipient,
  address _holder,
  uint256 _projectId,
  uint256 _amount
) external override requirePermission(_holder, _projectId, JBOperations.TRANSFER) { ... }
```

* Arguments:
  * `_recipient` is the recipient of the tokens.
  * `_holder` is the address to transfer tokens from.
  * `_projectId` is the ID of the project whose tokens are being transferred.
  * `_amount` is the amount of tokens to transfer.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.TRANSFER`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.

## Body

1.  Make sure a recipient was specified.

    ```solidity
    // Can't transfer to the zero address.
    if (_recipient == address(0)) {
      revert RECIPIENT_ZERO_ADDRESS();
    }
    ```
2.  Make sure the holder of the tokens isn't also the recipient.

    ```solidity
    // An address can't transfer to itself.
    if (_holder == _recipient) {
      revert INVALID_RECIPIENT();
    }
    ```
3.  Make sure there is amount to transfer.

    ```solidity
    // There must be an amount to transfer.
    if (_amount == 0) {
      revert TOKEN_AMOUNT_ZERO();
    }
    ```
4.  Get a reference to the amount of unclaimed tokens the holder has for the project.

    ```solidity
    // Get a reference to the amount of unclaimed tokens.
    uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
5.  Make sure the holder has enough unclaimed tokens to transfer.

    ```solidity
    // There must be enough unclaimed tokens to transfer.
    if (_amount > _unclaimedBalance) {
      revert INSUFFICIENT_UNCLAIMED_TOKENS();
    }
    ```
6.  Subtract the amount from the `unclaimedBalanceOf` the holder for the project.

    ```solidity
    // Subtract from the holder.
    unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
7.  Add the amount to the `unclaimedBalanceOf` the recipient for the project.

    ```solidity
    // Add the tokens to the recipient.
    unclaimedBalanceOf[_recipient][_projectId] =
      unclaimedBalanceOf[_recipient][_projectId] +
      _amount;
    ```

    _Internal references:_

    * [`unclaimedBalanceOf`](../properties/unclaimedbalanceof.md)
8.  Emit a `Transfer` event with the relevant parameters.

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
  Allows an unclaimed token holder to transfer them to another account, without claiming them to ERC-20s.

  @dev
  Only a token holder or an operator can transfer its unclaimed tokens.

  @param _recipient The recipient of the tokens.
  @param _holder The address to transfer tokens from.
  @param _projectId The ID of the project whose tokens are being transferred.
  @param _amount The amount of tokens to transfer.
*/
function transferTo(
  address _recipient,
  address _holder,
  uint256 _projectId,
  uint256 _amount
) external override requirePermission(_holder, _projectId, JBOperations.TRANSFER) {
  // Can't transfer to the zero address.
  if (_recipient == address(0)) {
    revert RECIPIENT_ZERO_ADDRESS();
  }

  // An address can't transfer to itself.
  if (_holder == _recipient) {
    revert INVALID_RECIPIENT();
  }

  // There must be an amount to transfer.
  if (_amount == 0) {
    revert TOKEN_AMOUNT_ZERO();
  }

  // Get a reference to the amount of unclaimed tokens.
  uint256 _unclaimedBalance = unclaimedBalanceOf[_holder][_projectId];

  // There must be enough unclaimed tokens to transfer.
  if (_amount > _unclaimedBalance) {
    revert INSUFFICIENT_UNCLAIMED_TOKENS();
  }

  // Subtract from the holder.
  unclaimedBalanceOf[_holder][_projectId] = unclaimedBalanceOf[_holder][_projectId] - _amount;

  // Add the tokens to the recipient.
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
| **`INVALID_RECIPIENT`**             | Thrown if the holder is the same address as the recipient.   |
| **`TOKEN_AMOUNT_ZERO`**             | Thrown if no amount was specified to transfer.               |
| **`INSUFFICIENT_UNCLAIMED_TOKENS`** | Thrown if the holder doesn't have enough tokens to transfer. |
{% endtab %}

{% tab title="Events" %}
| Name                                    | Data                                                                                                                                                                                                                        |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Transfer`**](../events/transfer.md) | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed recipient</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
