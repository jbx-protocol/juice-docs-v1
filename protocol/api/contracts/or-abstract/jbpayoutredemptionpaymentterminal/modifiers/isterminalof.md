# isTerminalOf

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**A modifier that verifies this terminal is a terminal of provided project ID.**

#### Definition

```solidity
modifier isTerminalOf(uint256 _projectId) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to check.

#### Body

1.  Make sure this terminal is a terminal of the specified project.

    ```solidity
    if (!directory.isTerminalOf(_projectId, this)) revert PROJECT_TERMINAL_MISMATCH();
    _;
    ```

    _External references:_

    * [`isTerminalOf`](../../../jbdirectory/read/isterminalof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  A modifier that verifies this terminal is a terminal of provided project ID.
*/
modifier isTerminalOf(uint256 _projectId) {
  if (!directory.isTerminalOf(_projectId, this)) revert PROJECT_TERMINAL_MISMATCH();
  _;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                       | Description                                             |
| ---------------------------- | ------------------------------------------------------- |
| **`PROJECT_TERMINAL_MISMATCH`** | Thrown if this terminal is not a terminal of the specified project. |

{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
