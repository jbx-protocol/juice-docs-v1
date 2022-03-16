# \_deriveStartFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The date that is the nearest multiple of the specified funding cycle's duration from its end.**

### Definition

```solidity
function _deriveStartFrom(JBFundingCycle memory _baseFundingCycle, uint256 _mustStartAtOrAfter)
  private
  pure
  returns (uint256 start) { ... }
```

* Arguments:
  * `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to base the calculation on.
  * `_mustStartAtOrAfter` is a date that the derived start must be on or come after.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns the next start time.

### Body

1.  A funding cycle with a duration of 0 can start as soon as possible.

    ```solidity
    // A subsequent cycle to one with a duration of 0 should start as soon as possible.
    if (_baseFundingCycle.duration == 0) return _mustStartAtOrAfter;
    ```
2.  Get a reference to the start time of the cycle immediately following the base cycle. This is the base cycle's start time plus the base cycle's duration.

    ```solidity
    // The time when the funding cycle immediately after the specified funding cycle starts.
    uint256 _nextImmediateStart = _baseFundingCycle.start + _baseFundingCycle.duration;
    ```
3.  If the next immediate start is allowed, it should be used. Otherwise, calculate a value depending on how much time has passed since the next immediate start.

    ```solidity
    // If the next immediate start is now or in the future, return it.
    if (_nextImmediateStart >= _mustStartAtOrAfter) return _nextImmediateStart;
    ```
4.  Save a reference to the amount of seconds since the time when the funding cycle must start on or after, which results in a start time that might satisfy the specified constraints.

    ```solidity
    // The amount of seconds since the `_mustStartAtOrAfter` time which results in a start time that might satisfy the specified constraints.
    uint256 _timeFromImmediateStartMultiple = (_mustStartAtOrAfter - _nextImmediateStart) %
      _baseFundingCycle.duration;
    ```
5.  Save a reference to the first possible start time.

    ```solidity
    // A reference to the first possible start timestamp.
    start = _mustStartAtOrAfter - _timeFromImmediateStartMultiple;
    ```
6.  It's possible that the start time doesn't satisfy the specified constraints. If so, add increments of the funding cycle's duration as necessary to satisfy the threshold.

    ```solidity
    // Add increments of duration as necessary to satisfy the threshold.
    while (_mustStartAtOrAfter > start) start = start + _baseFundingCycle.duration;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The date that is the nearest multiple of the specified funding cycle's duration from its end.

  @param _baseFundingCycle The funding cycle to base the calculation on.
  @param _mustStartAtOrAfter A date that the derived start must be on or come after.

  @return start The next start time.
*/
function _deriveStartFrom(JBFundingCycle memory _baseFundingCycle, uint256 _mustStartAtOrAfter)
  private
  pure
  returns (uint256 start)
{
  // A subsequent cycle to one with a duration of 0 should start as soon as possible.
  if (_baseFundingCycle.duration == 0) return _mustStartAtOrAfter;

  // The time when the funding cycle immediately after the specified funding cycle starts.
  uint256 _nextImmediateStart = _baseFundingCycle.start + _baseFundingCycle.duration;

  // If the next immediate start is now or in the future, return it.
  if (_nextImmediateStart >= _mustStartAtOrAfter) return _nextImmediateStart;

  // The amount of seconds since the `_mustStartAtOrAfter` time which results in a start time that might satisfy the specified constraints.
  uint256 _timeFromImmediateStartMultiple = (_mustStartAtOrAfter - _nextImmediateStart) %
    _baseFundingCycle.duration;

  // A reference to the first possible start timestamp.
  start = _mustStartAtOrAfter - _timeFromImmediateStartMultiple;

  // Add increments of duration as necessary to satisfy the threshold.
  while (_mustStartAtOrAfter > start) start = start + _baseFundingCycle.duration;
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
