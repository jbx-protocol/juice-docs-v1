# addTerminalOf

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Add a terminal to project's list of terminals.

  @dev
  Only a project owner, an operator, or its controller can add a terminal 

  @param _projectId The ID of the project having a terminal added.
  @param _terminal The terminal to add.
*/
function addTerminalOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.ADD_TERMINAL,
    msg.sender == address(controllerOf[_projectId])
  )
{
  // Can't set the zero address.
  require(_terminal != IJBTerminal(address(0)), 'ZERO_ADDRESS');

  // If the terminal is already set, nothing to do.
  if (isTerminalOf(_projectId, _terminal)) return;

  // Set the new terminal.
  _terminalsOf[_projectId].push(_terminal);

  emit AddTerminal(_projectId, _terminal, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}

{% endtab %}

{% tab title="Events" %}

{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}
