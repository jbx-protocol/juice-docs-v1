# addTerminalsOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Add terminals to project's list of terminals.**

_Only a project owner, an operator, or its controller can add terminals._

# Definition

```solidity
function addTerminalsOf(uint256 _projectId, IJBTerminal _terminal)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.ADD_TERMINALS,
    msg.sender == address(controllerOf[_projectId])
  ) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having a terminal added.
  * `_terminal` is the terminal to add.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.ADD_TERMINALS`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing.

# Body

1.  Add each terminal if it's not the zero address and it's not already added.

    ```solidity
    for (uint256 _i = 0; _i < _terminals.length; _i++) {
      // Can't be the zero address.
      if (_terminals[_i] == IJBTerminal(address(0))) {
        revert ADD_TERMINAL_ZERO_ADDRESS();
      }
      _addTerminalIfNeeded(_projectId, _terminals[_i]);
    }
    ```

    Internal references:

    * [`_addTerminalIfNeeded`](./_addterminalifneeded.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Add terminals to project's list of terminals.

  @dev
  Only a project owner, an operator, or its controller can add terminals.

  @param _projectId The ID of the project having a terminal added.
  @param _terminals The terminals to add.
*/
function addTerminalsOf(uint256 _projectId, IJBTerminal[] calldata _terminals)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.ADD_TERMINALS,
    msg.sender == address(controllerOf[_projectId])
  )
{
  for (uint256 _i = 0; _i < _terminals.length; _i++) {
    // Can't be the zero address.
    if (_terminals[_i] == IJBTerminal(address(0))) {
      revert ADD_TERMINAL_ZERO_ADDRESS();
    }

    _addTerminalIfNeeded(_projectId, _terminals[_i]);
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                                 |
| ------------------------ | ----------------------------------------------------------- |
| **`ADD_TERMINAL_ZERO_ADDRESS`** | Thrown if a provided terminal to add is the zero address. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
