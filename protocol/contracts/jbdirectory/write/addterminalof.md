# addTerminalOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Add a terminal to project's list of terminals.**

Only a project owner, an operator, or its controller can add a terminal.

# Definition

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
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.ADD_TERMINAL`permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing.

# Body

1.  Make sure the provided terminal isn't the zero address.

    ```solidity
    // Can't set the zero address.
    require(_terminal != IJBTerminal(address(0)), '0x2d: ZERO_ADDRESS');
    ```
2.  If the terminal is already in the project's list of terminals, there's nothing left to do.

    ```solidity
    // If the terminal is already in the project's list of terminals, return.
    if (isTerminalOf(_projectId, _terminal)) return;
    ```

    Internal references:

    * [`isTerminalOf`](../read/isterminalof.md)
3.  Add the terminal to the project's list of terminals.

    ```solidity
    // Set the new terminal.
    _terminalsOf[_projectId].push(_terminal);
    ```

    Internal references:

    * [`_terminalsOf`](../properties/\_terminalsof.md)
4.  Emit a `AddTerminal` event with the all relevant parameters.

    ```solidity
    emit AddTerminal(_projectId, _terminal, msg.sender);
    ```

    _Event references:_

    * [`AddTerminal`](../events/addterminal.md)
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
  require(_terminal != IJBTerminal(address(0)), '0x2d: ZERO_ADDRESS');

  // If the terminal is already in the project's list of terminals, return.
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
