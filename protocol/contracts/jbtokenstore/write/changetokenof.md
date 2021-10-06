# changeTokenOf

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Swap the current project's token that is minted and burned for another, and transfer ownership from the current to another address.

  @param _projectId The ID of the project to transfer tokens for.
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

  // If a new owner was provided, transfer ownership of the old token to the new owner.
  if (_newOwner != address(0)) _currentToken.transferOwnership(_newOwner);

  emit ChangeToken(_projectId, _token, _newOwner, msg.sender);
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}

