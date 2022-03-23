# currentOverflowOf

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Gets the current overflowed amount in a terminal for a specified project.**

_The current overflow is represented as a fixed point number with the same amount of decimals as the specified terminal._

#### Definition

```solidity
function currentOverflowOf(IJBPaymentTerminal _terminal, uint256 _projectId)
  external
  view
  override
  returns (uint256) { ... }
```

* Arguments:
  * `_terminal` is the terminal for which the overflow is being calculated.
  * `_projectId` is the ID of the project to get overflow for.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The resulting function overrides a function definition from the [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md) interface.
* The function returns the current amount of overflow that project has in the specified terminal.

#### Body

1.  Forward the call to the internal version of the function that is also used by other operations, using the project's current funding cycle.

    ```solidity
    // Return the overflow during the project's current funding cycle.
    return
      _overflowDuring(
        _terminal,
        _projectId,
        fundingCycleStore.currentOf(_projectId),
        _terminal.currency()
      );
    ```

    _Internal references:_

    * [`_overflowDuring`](_overflowduring.md)

    _External references:_

    * [`currentOf`](../../jbfundingcyclestore/read/currentof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the current overflowed amount in a terminal for a specified project.

  @dev
  The current overflow is represented as a fixed point number with the same amount of decimals as the specified terminal.

  @param _terminal The terminal for which the overflow is being calculated.
  @param _projectId The ID of the project to get overflow for.

  @return The current amount of overflow that project has in the specified terminal.
*/
function currentOverflowOf(IJBPaymentTerminal _terminal, uint256 _projectId)
  external
  view
  override
  returns (uint256)
{
  // Return the overflow during the project's current funding cycle.
  return
    _overflowDuring(
      _terminal,
      _projectId,
      fundingCycleStore.currentOf(_projectId),
      _terminal.currency()
    );
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
