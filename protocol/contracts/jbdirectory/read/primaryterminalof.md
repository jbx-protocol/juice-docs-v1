# primaryTerminalOf

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  The primary terminal that is managing funds for a project for a specified token.

  @param _projectId The ID of the project to get a terminal for.
  @param _token The token the terminal accepts.

  @return The primary terminal for the project for the specified token.
*/
function primaryTerminalOf(uint256 _projectId, address _token)
  public
  view
  override
  returns (IJBTerminal)
{
  // If a primary terminal for the token was specifically set, return it.
  if (_primaryTerminalOf[_projectId][_token] != IJBTerminal(address(0)))
    return _primaryTerminalOf[_projectId][_token];

  // return the first terminal which accepts the specified token.
  for (uint256 _i; _i < _terminalsOf[_projectId].length; _i++) {
    IJBTerminal _terminal = _terminalsOf[_projectId][_i];
    if (_terminal.vault().token() == _token) return _terminal;
  }

  // Not found.
  return IJBTerminal(address(0));
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}
