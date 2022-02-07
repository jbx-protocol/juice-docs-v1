# changeFor

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Swap the current project's token that is minted and burned for another, and transfer ownership of the current token to another address if needed.**

_Only a project's current controller can change its token._

_This JBTokenStore contract must have access to all IJBToken interface functions._

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
  * `_token` is the new token, which must adhere to the [`IJBToken`](../../../interfaces/ijbtoken.md) specification.
  * `_newOwner` is an address to transfer the current token's ownership to. This is optional, but it cannot be done later.
* Through the [`onlyController`](../../or-abstract/jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.

#### Body

1.  Get a reference to the project's current token.

    ```solidity
    // Get a reference to the current owner of the token.
    IJBToken _currentToken = tokenOf[_projectId];
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
2.  Store the provided token as the `tokenOf` the project.

    ```solidity
    // Store the new token.
    tokenOf[_projectId] = _token;
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
3.  If there's a current token and a new owner address was provided, transfer the ownership of the current token from this contract to the new owner. This will let the new owner mint and burn tokens from the current token contract.

    ```solidity
    // If there's a current token and a new owner was provided, transfer ownership of the old token to the new owner.
    if (_currentToken != IJBToken(address(0)) && _newOwner != address(0))
      _currentToken.transferOwnership(_newOwner);
    ```

    _External references:_

    * [`transferOwnership`](../../jbtoken/write/transferownership.md)
4.  Emit a `Change` event with the relevant parameters.

    ```solidity
    emit Change(_projectId, _token, _newOwner, msg.sender);
    ```

    _Event references:_

    * [`Change`](../events/change.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Swap the current project's token that is minted and burned for another, and transfer ownership of the current token to another address if needed.

  @dev
  Only a project's current controller can change its token.

  @dev
  This JBTokenStore contract must have access to all IJBToken interface functions.

  @param _projectId The ID of the project to which the changed token belongs.
  @param _token The new token.
  @param _newOwner An address to transfer the current token's ownership to. This is optional, but it cannot be done later.
*/
function changeFor(
  uint256 _projectId,
  IJBToken _token,
  address _newOwner
) external override onlyController(_projectId) {
  // Get a reference to the current owner of the token.
  IJBToken _currentToken = tokenOf[_projectId];

  // Store the new token.
  tokenOf[_projectId] = _token;

  // If there's a current token and a new owner was provided, transfer ownership of the old token to the new owner.
  if (_newOwner != address(0) && _currentToken != IJBToken(address(0)))
    _currentToken.transferOwnership(_newOwner);

  emit Change(_projectId, _token, _newOwner, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                                                                                                                                |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Change`**](../events/change.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>IJBToken indexed token</code></li><li><code>address indexed owner</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
