# changeFor

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Swap the current project's token for another, and transfer ownership of the current token to another address if needed.**

_Only a project's current controller can change its token._

_This contract must have access to all of the token's [`IJBToken`](../../../interfaces/ijbtoken.md) interface functions._

#### Definition

```solidity
function changeFor(
  uint256 _projectId,
  IJBToken _token,
  address _newOwner
) external override onlyController(_projectId) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the changed token belongs.
  * `_token` is the new token. Send an empty address to remove the project's current token without adding a new one.
  * `_newOwner` is an address to transfer the current token's ownership to. This is optional, but it cannot be done later.
* Through the [`onlyController`](../../or-abstract/jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) interface.
* The function doesn't return anything.

#### Body

1.  Get a reference to the project's current token.

    ```solidity
    // Get a reference to the current token for the project.
    oldToken = tokenOf[_projectId];
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
2.  Store the provided token as the token of the project.

    ```solidity
    // Store the new token.
    tokenOf[_projectId] = _token;
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
3.  If there's a current token and a new owner address was provided, transfer the ownership of the current token from this contract to the new owner.

    ```solidity
    // If there's a current token and a new owner was provided, transfer ownership of the old token to the new owner.
    if (_newOwner != address(0) && oldToken != IJBToken(address(0)))
      oldToken.transferOwnership(_newOwner);
    ```

    _External references:_

    * [`transferOwnership`](../../jbtoken/write/transferownership.md)
4.  Emit a `Change` event with the relevant parameters.

    ```solidity
    emit Change(_projectId, _token, oldToken, _newOwner, msg.sender);
    ```

    _Event references:_

    * [`Change`](../events/change.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Swap the current project's token for another, and transfer ownership of the current token to another address if needed.

  @dev
  Only a project's current controller can change its token.

  @dev
  This contract must have access to all of the token's `IJBToken` interface functions.

  @param _projectId The ID of the project to which the changed token belongs.
  @param _token The new token. Send an empty address to remove the project's current token without adding a new one.
  @param _newOwner An address to transfer the current token's ownership to. This is optional, but it cannot be done later.

  @return oldToken The token that was removed as the project's token.
*/
function changeFor(
  uint256 _projectId,
  IJBToken _token,
  address _newOwner
) external override onlyController(_projectId) returns (IJBToken oldToken) {
  // Get a reference to the current token for the project.
  oldToken = tokenOf[_projectId];

  // Store the new token.
  tokenOf[_projectId] = _token;

  // If there's a current token and a new owner was provided, transfer ownership of the old token to the new owner.
  if (_newOwner != address(0) && oldToken != IJBToken(address(0)))
    oldToken.transferOwnership(_newOwner);

  emit Change(_projectId, _token, oldToken, _newOwner, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                                                                                                                                |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Change`**](../events/change.md)               | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBToken`](../../../interfaces/ijbtoken.md)indexed newToken</code></li><li><code>[`IJBToken`](../../../interfaces/ijbtoken.md)indexed oldToken</code></li><li><code>address indexed owner</code></li><li><code>address caller</code></li></ul>                                                                                           |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
