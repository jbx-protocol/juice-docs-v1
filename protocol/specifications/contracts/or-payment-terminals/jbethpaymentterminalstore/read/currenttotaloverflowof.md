# currentTotalOverflowOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Gets the current overflowed amount (in the terminal's currency) for a specified project across all terminals.**

### Definition

```solidity
function currentOverflowOf(uint256 _projectId) external view returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get total overflow for.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function returns the current total amount of overflow that project has across all terminals.

### Body

1.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentOf.md)
2.  Return the total overflow given the state of the current funding cycle.

    ```solidity
    return _totalOverflowDuring(_projectId, _fundingCycle);
    ```

    _Internal references:_

    * [`_totalOverflowDuring`](\_totaloverflowDuring.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the current overflowed amount (in the terminal's currency) for a specified project across all terminals.

  @param _projectId The ID of the project to get total overflow for.

  @return The current total amount of overflow that project has across all terminals.
*/
function currentTotalOverflowOf(uint256 _projectId) external view returns (uint256) {
  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  return _totalOverflowDuring(_projectId, _fundingCycle);
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
