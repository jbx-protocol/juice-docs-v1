# currentOverflowOf

Contract: [`JBETHPaymentTerminalStore`](broken-reference)​‌

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflowed ETH that can be claimed by the specified number of tokens.**

_If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used._

## Definition

```solidity
function currentOverflowOf(uint256 _projectId) external view returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get overflow for.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function returns the current amount of overflow that project has.

## Body

1.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentOf.md)
2.  Return the overflow given the state of the current funding cycle.

    ```solidity
    return _overflowDuring(_fundingCycle);
    ```

    _Internal references:_

    * [`_overflowDuring`](\_overflowDuring.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the current overflowed amount for a specified project.

  @param _projectId The ID of the project to get overflow for.

  @return The current amount of overflow that project has.
*/
function currentOverflowOf(uint256 _projectId) external view returns (uint256) {
  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  return _overflowFrom(_fundingCycle);
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
