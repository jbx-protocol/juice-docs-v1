# _addTerminalIfNeeded

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Add a terminal to a project's list of terminals if it hasn't been already.**

### Definition

```solidity
function _addTerminalIfNeeded(uint256 _projectId, IJBPaymentTerminal _terminal) private { ... }
```

* Arguments:
  * `_projectId` is the ID of the project having a terminal added.
  * `_terminal` is the terminal to add.
* The function is private to this contract.
* The function doesn't return anything.

### Body

1.  Nothing to do if the terminal is already a terminal of the project.

    ```solidity
    // Check that the terminal has not already been added.
    if (isTerminalOf(_projectId, _terminal)) return;
    ```

    Internal references:

    * [`isTerminalOf`](../read/isterminalof.md)
2.  Add the terminal.

    ```solidity
    // Add the new terminal.
    _terminalsOf[_projectId].push(_terminal);
    ```

    Internal references:

    * [`_terminalsOf`](../properties/_terminalsof.md)
3.  Emit a `AddTerminal` event with the relevant parameters.

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
  Add a terminal to a project's list of terminals if it hasn't been already.

  @param _projectId The ID of the project having a terminal added.
  @param _terminal The terminal to add.
*/
function _addTerminalIfNeeded(uint256 _projectId, IJBPaymentTerminal _terminal) private {
  // Check that the terminal has not already been added.
  if (isTerminalOf(_projectId, _terminal)) return;

  // Add the new terminal.
  _terminalsOf[_projectId].push(_terminal);

  emit AddTerminal(_projectId, _terminal, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                          | Data                                                                                                                                                                                                        |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`AddTerminal`**](../events/addterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../../interfaces/ijbpaymentterminal.md"><code>IJBPaymentTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
