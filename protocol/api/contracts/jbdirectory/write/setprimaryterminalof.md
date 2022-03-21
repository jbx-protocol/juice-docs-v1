# setPrimaryTerminalOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Project's can set which terminal should be their primary for a particular token.**

**This is useful in case a project has several terminals connected for a particular token.**

_The terminal will be set as the primary terminal where ecosystem contracts should route tokens._

### Definition

```solidity
function setPrimaryTerminalOf(uint256 _projectId, IJBPaymentTerminal _terminal)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.SET_PRIMARY_TERMINAL) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project for which a primary token is being set.
  * `_terminal` is the terminal to make primary.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the [`JBOperations.SET_PRIMARY_TERMINAL`](../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the [`IJBDirectory`](../../../interfaces/ijbdirectory.md) interface.
* The function doesn't return anything.

### Body

1.  Get a reference to the token that the provided terminal's vault accepts.

    ```solidity
    // Get a reference to the token that the terminal accepts.
    address _token = _terminal.token();
    ```

    _External references:_

    * [`token`](../../or-payment-terminals/jbethpaymentterminal/properties/token.md)
2.  Add the terminal to the list of the project's terminals if it isn't included already.

    ```solidity
    // Add the terminal to the project if it hasn't been already.
    _addTerminalIfNeeded(_projectId, _terminal);
    ```

    Internal references:

    * [`_addTerminalIfNeeded`](\_addTerminalIfNeeded.md)
3.  Store the new terminal as the primary.

    ```solidity
    // Store the terminal as the primary for the particular token.
    _primaryTerminalOf[_projectId][_token] = _terminal;
    ```

    Internal references:

    * [`_primaryTerminalOf`](../read/primaryTerminalOf.md)
4.  Emit a `SetPrimaryTerminal` event with the relevant parameters.

    ```solidity
    emit SetPrimaryTerminal(_projectId, _token, _terminal, msg.sender);
    ```

    _Event references:_

    * [`SetPrimaryTerminal`](../events/setprimaryterminalmd/)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Project's can set which terminal should be their primary for a particular token.
  This is useful in case a project has several terminals connected for a particular token.

  @dev
  The terminal will be set as the primary terminal where ecosystem contracts should route tokens.

  @param _projectId The ID of the project for which a primary token is being set.
  @param _terminal The terminal to make primary.
*/
function setPrimaryTerminalOf(uint256 _projectId, IJBPaymentTerminal _terminal)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.SET_PRIMARY_TERMINAL)
{
  // Get a reference to the token that the terminal accepts.
  address _token = _terminal.token();

  // Add the terminal to the project if it hasn't been already.
  _addTerminalIfNeeded(_projectId, _terminal);

  // Store the terminal as the primary for the particular token.
  _primaryTerminalOf[_projectId][_token] = _terminal;

  emit SetPrimaryTerminal(_projectId, _token, _terminal, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                                        | Data                                                                                                                                                                                                                                                   |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`SetPrimaryTerminal`**](events/setprimaryterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed token</code></li><li><code>[`IJBPaymentTerminal`](../../interfaces/ijbpaymentterminal.md)indexed terminal</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
