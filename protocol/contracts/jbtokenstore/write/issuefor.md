# issueFor

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Issues an owner's ERC-20 Tokens that'll be used when unstaking tokens.

  @dev 
  Deploys an owner's Token ERC-20 token contract.

  @param _projectId The ID of the project being issued tokens.
  @param _name The ERC-20's name.
  @param _symbol The ERC-20's symbol.
*/
function issueFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.ISSUE)
  returns (IJBToken token)
{
  // There must be a name.
  require((bytes(_name).length > 0), '0x1f: EMPTY_NAME');

  // There must be a symbol.
  require((bytes(_symbol).length > 0), '0x20: EMPTY_SYMBOL');

  // Only one ERC20 token can be issued.
  require(tokenOf[_projectId] == IJBToken(address(0)), '0x21: ALREADY_ISSUED');

  // Deploy the token contract.
  token = new JBToken(_name, _symbol);

  // Store the token contract.
  tokenOf[_projectId] = token;

  emit Issue(_projectId, token, _name, _symbol, msg.sender);
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}

