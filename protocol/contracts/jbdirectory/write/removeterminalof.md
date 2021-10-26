# removeTerminalOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Remove a terminal from a project's list of terminals.**

_Only a project owner or an operator can remove one of its terminals._

## Definition

```solidity
function removeTerminalOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REMOVE_TERMINAL) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having a terminal removed.
  * `_terminal` is the terminal to remove.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.REMOVE_TERMINAL`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing.

## Body

1.  Get a reference to the project's terminals.

    ```solidity
    // Get a reference to the terminals of the project.
    IJBTerminal[] memory _terminals = _terminalsOf[_projectId];
    ```

    Internal references:

    * [`_terminalsOf`](../properties/\_terminalsof.md)
2.  Delete all terminals from storage. All terminals except the one being removed will later be repupulated.

    ```solidity
    // Delete the stored terminals for the project.
    delete _terminalsOf[_projectId];
    ```

    Internal references:

    * [`_terminalsOf`](../properties/\_terminalsof.md)
3.  Loop through the terminals, adding all terminals that aren't the one being removed back into storage.

    ```solidity
    // Repopulate the stored terminals for the project, omitting the one being deleted.
    for (uint256 _i; _i < _terminals.length; _i++)
      // Don't include the terminal being deleted.
      if (_terminals[_i] != _terminal) _terminalsOf[_projectId].push(_terminals[_i]);
    ```

    Internal references:

    * [`_terminalsOf`](../properties/\_terminalsof.md)
4.  If the terminal being removed is set to be the primary terminal for its vault's token, delete it from being the primary terminal.

    ```solidity
    // If the terminal that is being removed is the primary terminal for the token, delete it from being primary terminal.
    if (_primaryTerminalOf[_projectId][_terminal.vault().token()] == _terminal)
      delete _primaryTerminalOf[_projectId][_terminal.vault().token()];
    ```

    Internal references:

    * [`_primaryTerminalOf`](../read/\_primaryterminalof.md)
5.  Emit a `RemoveTerminal` event with the all relevant parameters.

    ```solidity
    emit RemoveTerminal(_projectId, _terminal, msg.sender);
    ```

```solidity
// If the terminal that is being removed is the primary terminal for the token, delete it from being primary terminal.
if (_primaryTerminalOf[_projectId][_terminal.token()] == _terminal)
  delete _primaryTerminalOf[_projectId][_terminal.token()];
```

```
* [`RemoveTerminal`](../events/removeterminal.md)
```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Remove a terminal from a project's list of terminals.

  @dev
  Only a project owner or an operator can remove one of its terminals. 

  @param _projectId The ID of the project having a terminal removed.
  @param _terminal The terminal to remove.
*/
function removeTerminalOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REMOVE_TERMINAL)
{
  // Get a reference to the terminals of the project.
  IJBTerminal[] memory _terminals = _terminalsOf[_projectId];

  // Delete the stored terminals for the project.
  delete _terminalsOf[_projectId];

  // Repopulate the stored terminals for the project, omitting the one being deleted.
  for (uint256 _i; _i < _terminals.length; _i++)
    // Don't include the terminal being deleted.
    if (_terminals[_i] != _terminal) _terminalsOf[_projectId].push(_terminals[_i]);

  // If the terminal that is being removed is the primary terminal for the token, delete it from being primary terminal.
  if (_primaryTerminalOf[_projectId][_terminal.token()] == _terminal)
    delete _primaryTerminalOf[_projectId][_terminal.token()];

  emit RemoveTerminal(_projectId, _terminal, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                                | Data                                                                                                                                                                                                     |
| --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`RemoveTerminal`**](../events/removeterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
