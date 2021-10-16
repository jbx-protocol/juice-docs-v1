# removeTerminalOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Remove a terminal from a project's list of terminals.**

_Only a project owner or an operator can remove one of its terminals._

Definition:

```solidity
function removeTerminalOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REMOVE_TERMINAL) { ... }
```

* Arguments:
  * `_holder` is the address address that owns the tokens being burned.
  * `_projectId` is the ID of the project to which the burned tokens belong.
  * `_amount` is the amount of tokens to burn.
  * `_preferClaimedTokens` is a flag indicating if there's a preference to burn tokens that have been converted to ERC-20s.
* Through the [`onlyController`](../../jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.
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
  if (_primaryTerminalOf[_projectId][_terminal.vault().token()] == _terminal)
    delete _primaryTerminalOf[_projectId][_terminal.vault().token()];

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
