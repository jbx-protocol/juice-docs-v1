# setTerminalsOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Set a project's terminals.**

_Only a project owner, an operator, or its controller can set its terminals._

### Definition

```solidity
function setTerminalsOf(uint256 _projectId, IJBPaymentTerminal[] calldata _terminals)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_TERMINALS,
    msg.sender == address(controllerOf[_projectId])
  ) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having terminals set.
  * `_terminals` is the terminals to set.
* Through the [`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.SET_TERMINALS` permission by the project owner for the provided `_projectId`, or by the project's controller.
* The function overrides a function definition from the [`IJBDirectory`](../../../interfaces/ijbdirectory.md) interface.
* The function doesn't return anything.

### Body

1.  Get a reference to the project's current terminals.

    ```solidity
    // Get a reference to the terminals of the project.
    IJBPaymentTerminal[] memory _oldTerminals = _terminalsOf[_projectId];
    ```

    Internal references:

    * [`_terminalsOf`](../properties/_termialsof.md)
2.  Delete the project's current set of terminals from storage.

    ```solidity
    // Delete the stored terminals for the project.
    _terminalsOf[_projectId] = _terminals;
    ```

    Internal references:

    * [`_terminalsOf`](../properties/_termialsof.md)
3.  If any of the project's primary terminals are being removed, also remove their status as a primary terminal.

    ```solidity
    // If one of the old terminals was set as a primary terminal but is not included in the new terminals, remove it from being a primary terminal.
    for (uint256 _i; _i < _oldTerminals.length; _i++)
      if (
        _primaryTerminalOf[_projectId][_oldTerminals[_i].token()] == _oldTerminals[_i] &&
        !_contains(_terminals, _oldTerminals[_i])
      ) delete _primaryTerminalOf[_projectId][_oldTerminals[_i].token()];
    ```

    Internal references:

    * [`_primaryTerminalOf`](../properties/_primaryterminalof.md)
    * [`_contains`](../read/_contains.md)
4.  Emit a `SetTerminals` event with the relevant parameters.

    ```solidity
    emit SetTerminals(_projectId, _terminals, msg.sender);
    ```

    _Event references:_

    * [`SetTerminals`](../events/setterminals.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Set a project's terminals.

  @dev
  Only a project owner, an operator, or its controller can set its terminals. 

  @param _projectId The ID of the project having terminals set.
  @param _terminals The terminal to set.
*/
function setTerminalsOf(uint256 _projectId, IJBPaymentTerminal[] calldata _terminals)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_TERMINALS,
    msg.sender == address(controllerOf[_projectId])
  )
{
  // Get a reference to the terminals of the project.
  IJBPaymentTerminal[] memory _oldTerminals = _terminalsOf[_projectId];

  // Delete the stored terminals for the project.
  _terminalsOf[_projectId] = _terminals;

  // If one of the old terminals was set as a primary terminal but is not included in the new terminals, remove it from being a primary terminal.
  for (uint256 _i; _i < _oldTerminals.length; _i++)
    if (
      _primaryTerminalOf[_projectId][_oldTerminals[_i].token()] == _oldTerminals[_i] &&
      !_contains(_terminals, _oldTerminals[_i])
    ) delete _primaryTerminalOf[_projectId][_oldTerminals[_i].token()];

  emit SetTerminals(_projectId, _terminals, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                               |
| ------------------------------- | --------------------------------------------------------- |
| **`ADD_TERMINAL_ZERO_ADDRESS`** | Thrown if a provided terminal to add is the zero address. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                          | Data                                                                                          |
| ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [**`SetTerminals`**](events/setterminals.md)         | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)[] indexed terminals</code></li><li><code>address caller</code></li></ul>                                            |
{% endtab %}


{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
