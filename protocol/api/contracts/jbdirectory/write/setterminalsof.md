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
* Through the [`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the [`JBOperations.SET_TERMINALS`](../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`, or by the project's controller.
* The function overrides a function definition from the [`IJBDirectory`](../../../interfaces/ijbdirectory.md) interface.
* The function doesn't return anything.

### Body

1.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../jbfundingcyclestore/read/currentof.md)

2.  Make sure the project's current funding cycle is set to allow setting terminals, or the request to set the terminals is coming from the project's current controller.

    ```solidity
    // Setting terminals must be allowed if not called from the current controller.
    if (msg.sender != address(controllerOf[_projectId]) && !_fundingCycle.setTerminalsAllowed())
      revert SET_TERMINALS_NOT_ALLOWED();
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.setTerminalsAllowed(...)`

    _Internal references:_

    * [`controllerOf`](../properties/controllerof.md)

3.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../jbfundingcyclestore/read/currentof.md)

4.  Get a reference to the project's current terminals.

    ```solidity
    // Get a reference to the terminals of the project.
    IJBPaymentTerminal[] memory _oldTerminals = _terminalsOf[_projectId];
    ```

    _Internal references:_

    * [`_terminalsOf`](../properties/_terminalsof.md)
5.  Delete the project's current set of terminals from storage.

    ```solidity
    // Delete the stored terminals for the project.
    _terminalsOf[_projectId] = _terminals;
    ```

    _Internal references:_

    * [`_terminalsOf`](../properties/_terminalsof.md)

6.  Make sure the same terminal isn't being set multiple times.
    ```solidity
    // Make sure duplicates were not added.
    if (_terminals.length > 1)
      for (uint256 _i; _i < _terminals.length; _i++)
        for (uint256 _j = _i + 1; _j < _terminals.length; _j++)
          if (_terminals[_i] == _terminals[_j]) revert DUPLICATE_TERMINALS();
    ```

7.  If any of the project's primary terminals are being removed, also remove their status as a primary terminal.

    ```solidity
    // If one of the old terminals was set as a primary terminal but is not included in the new terminals, remove it from being a primary terminal.
    for (uint256 _i; _i < _oldTerminals.length; _i++)
      if (
        _primaryTerminalOf[_projectId][_oldTerminals[_i].token()] == _oldTerminals[_i] &&
        !_contains(_terminals, _oldTerminals[_i])
      ) delete _primaryTerminalOf[_projectId][_oldTerminals[_i].token()];
    ```

    _Internal references:_

    * [`_primaryTerminalOf`](../properties/_primaryterminalof.md)
    * [`_contains`](../read/_contains.md)
8.  Emit a `SetTerminals` event with the relevant parameters.

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
  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Setting terminals must be allowed if not called from the current controller.
  if (msg.sender != address(controllerOf[_projectId]) && !_fundingCycle.setTerminalsAllowed())
    revert SET_TERMINALS_NOT_ALLOWED();

  // Get a reference to the terminals of the project.
  IJBPaymentTerminal[] memory _oldTerminals = _terminalsOf[_projectId];

  // Delete the stored terminals for the project.
  _terminalsOf[_projectId] = _terminals;

  // Make sure duplicates were not added.
  if (_terminals.length > 1)
    for (uint256 _i; _i < _terminals.length; _i++)
      for (uint256 _j = _i + 1; _j < _terminals.length; _j++)
        if (_terminals[_i] == _terminals[_j]) revert DUPLICATE_TERMINALS();

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
| **`DUPLICATE_TERMINALS`** | Thrown if the same terminal is being set multiple times. |
| **`SET_TERMINALS_NOT_ALLOWED`**          | Thrown if the provided project isn't currently allowed to set its terminals.                |
{% endtab %}

{% tab title="Events" %}
| Name                                                                          | Data                                                                                          |
| ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [**`SetTerminals`**](../events/setterminals.md)         | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)[] indexed terminals</code></li><li><code>address caller</code></li></ul>                                            |
{% endtab %}


{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
