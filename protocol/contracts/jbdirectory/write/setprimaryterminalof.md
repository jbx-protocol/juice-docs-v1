# setPrimaryTerminalOf

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Project's can set which terminal should be their primary for a particular token.
  This is useful in case a project has several terminals connected for a particular token.

  @dev
  The terminal will be set as the primary for the token that it's vault accepts. 

  @param _projectId The ID of the project for which a primary token is being set.
  @param _terminal The terminal to make primary.
*/
function setPrimaryTerminalOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.SET_PRIMARY_TERMINAL)
{
  // Get a reference to the token that the terminal's vault accepts.
  address _token = _terminal.vault().token();

  // Store the terminal as the primary for the particular token.
  _primaryTerminalOf[_projectId][_token] = _terminal;

  emit SetPrimaryTerminal(_projectId, _token, _terminal, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                         | Description                                              |
| ------------------------------ | -------------------------------------------------------- |
| **`0x2d: ZERO_ADDRESS`** | Thrown if the provided terminal to add is the zero address. |
{% endtab %}
{% tab title="Events" %}

{% endtab %}

{% tab title="Events" %}
| Name                            | Data                                                                                                                                                                                                                                                                          |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetPrimaryTerminal`**](../events/setprimaryterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed token</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul> |
{% endtab %}
{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}
