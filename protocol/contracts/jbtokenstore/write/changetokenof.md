# changeTokenOf

Contract: [`JBTokenStore`](broken-reference)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Swap the current project's token that is minted and burned for another, and transfer ownership from the current to another address.**

_Only a project owner or operator can change its token._

# Definition

```solidity
function changeTokenOf(
  uint256 _projectId,
  IJBToken _token,
  address _newOwner
)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.CHANGE_TOKEN) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the changed token belongs.
  * `_token` is the new token, which must adhere to the [`IJBToken`](../../../interfaces/ijbtoken.md) specification.
  * `_newOwner` is an address to transfer the current token's ownership to. This is optional, but it cannot be done later.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.CHANGE_TOKEN`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.

# Body

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
4.  Emit a `ChangeToken` event with the relevant parameters.

    ```solidity
    emit ChangeToken(_projectId, _token, _newOwner, msg.sender);
    ```

    _Event references:_

    * [`ChangeToken`](../events/changetoken.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Swap the current project's token that is minted and burned for another, and transfer ownership from the current to another address.

  @dev
  Only a project owner or operator can change its token.

  @param _projectId The ID of the project to which the changed token belongs.
  @param _token The new token.
  @param _newOwner An address to transfer the current token's ownership to. This is optional, but it cannot be done later.
*/
function changeTokenOf(
  uint256 _projectId,
  IJBToken _token,
  address _newOwner
)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.CHANGE_TOKEN)
{
  // Get a reference to the current owner of the token.
  IJBToken _currentToken = tokenOf[_projectId];

  // Store the new token.
  tokenOf[_projectId] = _token;

  // If there's a current token and a new owner was provided, transfer ownership of the old token to the new owner.
  if (_newOwner != address(0) && _currentToken != IJBToken(address(0)))
    _currentToken.transferOwnership(_newOwner);

  emit ChangeToken(_projectId, _token, _newOwner, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                          | Data                                                                                                                                                                                |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`ChangeToken`**](../events/usenewtoken.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>IJBToken indexed token</code></li><li><code>address indexed owner</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
