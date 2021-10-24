# \_subtractFromTokenTrackerOf

{% tabs %}
{% tab title="Step by step" %}
**Subtracts the provided value from the processed token tracker.**

## Definition

```solidity
function _subtractFromTokenTrackerOf(uint256 _projectId, uint256 _amount) private { ... }
```

* Arguments:
  * `_projectId` is the ID of the project that is having its tracker subtracted from.
  * `_amount` is the amount to subtract.
* The function is private to this contract.
* The function doesn't return anything.

## Body

TODO
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice
  Subtracts the provided value from the processed token tracker.

  @dev
  Necessary to account for both positive and negative values.

  @param _projectId The ID of the project that is having its tracker subtracted from.
  @param _amount The amount to subtract.

*/
function _subtractFromTokenTrackerOf(uint256 _projectId, uint256 _amount) private {
  // Get a reference to the processed token tracker for the project.
  int256 _processedTokenTracker = _processedTokenTrackerOf[_projectId];

  // Subtract the count from the processed token tracker.
  // If there are at least as many processed tokens as the specified amount,
  // the processed token tracker of the project will be positive. Otherwise it will be negative.
  _processedTokenTrackerOf[_projectId] = _processedTokenTracker < 0 // If the tracker is negative, add the count and reverse it.
    ? -int256(uint256(-_processedTokenTracker) + _amount) // the tracker is less than the count, subtract it from the count and reverse it.
    : _processedTokenTracker < int256(_amount)
    ? -(int256(_amount) - _processedTokenTracker) // simply subtract otherwise.
    : _processedTokenTracker - int256(_amount);
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
