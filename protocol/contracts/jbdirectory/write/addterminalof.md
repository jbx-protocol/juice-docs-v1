# addTerminalOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Whether or not a specified terminal is a terminal of the specified project.**

_Only a project owner, an operator, or its controller can add a terminal. _

Definition:

```solidity
function addTerminalOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.ADD_TERMINAL,
    msg.sender == address(controllerOf[_projectId])
  ) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having a terminal added.
  * `_terminal` is the terminal to add.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing.
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Add a terminal to project's list of terminals.

  @dev
  Only a project owner, an operator, or its controller can add a terminal. 

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
| String                   | Description                                                 |
| ------------------------ | ----------------------------------------------------------- |
| **`0x2d: ZERO_ADDRESS`** | Thrown if the provided terminal to add is the zero address. |
{% endtab %}

{% tab title="Events" %}
| Name                                          | Data                                                                                                                                                                                                     |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`AddTerminal`**](../events/addterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
