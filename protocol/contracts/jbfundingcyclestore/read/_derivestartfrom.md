# \_deriveStartFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The date that is the nearest multiple of the specified funding cycle's duration from its end.**

# Definition

```solidity
function _deriveStartFrom(
  JBFundingCycle memory _baseFundingCycle,
  uint256 _mustStartOnOrAfter
) private pure returns (uint256 start) { ... }
```

* Arguments:
  * `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
  * `_mustStartOnOrAfter` is a date that the derived start must be on or come after.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns a timestamp in seconds.


# Body 

1.  A funding cycle with a duration of 0 can start as soon as possible. 

    ```solidity
    // A subsequent cycle to one with a duration of 0 should start as soon as possible.
    if (_baseFundingCycle.duration == 0) return _mustStartOnOrAfter;
    ```


2.  Get a reference to the duration of the base cycle in seconds.\

    ```solidity
    // Save a reference to the cycle's duration measured in seconds.
    uint256 _cycleDurationInSeconds = _baseFundingCycle.duration * _SECONDS_IN_DAY;
    ```

    _Internal references:_

    * [`_SECONDS_IN_DAY`](../properties/\_seconds_in_day.md)


3.  Get a reference to the start time of the cycle immediately following the base cycle. This is the base cycles start time plus the base cycle's duration.

    ```solidity
    // The time when the funding cycle immediately after the specified funding cycle starts.
    uint256 _nextImmediateStart = _baseFundingCycle.start + _baseCycleDurationInSeconds;
    ```


4.  If the `_nextImmediateStart` is allowed, it should be used. Otherwise we'll have to calculate a value depending on how much time has passed since the `_nextImmediateStart`.

    ```solidity
    // If the next immediate start is now or in the future, return it.
    if (_nextImmediateStart >= _mustStartOnOrAfter) return _nextImmediateStart;
    ```


5.  Save a reference to the amount of seconds since the `_mustStartOnOrAfter` time that results in a start time that might satisfy the specified constraints.

    ```solidity
    // The amount of seconds since the `_mustStartOnOrAfter` time that results in a start time that might satisfy the specified constraints.
    uint256 _timeFromImmediateStartMultiple = (_mustStartOnOrAfter - _nextImmediateStart) %
      _cycleDurationInSeconds;
    ```


6.  Save a reference to the first possible start time.

    ```solidity
    // A reference to the first possible start timestamp.
    start = _mustStartOnOrAfter - _timeFromImmediateStartMultiple;
    ```


7.  It's possible that the `start` time doesn't satisfy the specified constraints. If so, add increments of the funding cycle's duration as necessary to satisfy the threshold.

    ```solidity
    // Add increments of duration as necessary to satisfy the threshold.
    while (_mustStartOnOrAfter > start) start = start + _cycleDurationInSeconds;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The date that is the nearest multiple of the specified funding cycle's duration from its end.

  @param _baseFundingCycle The funding cycle to make the calculation for.
  @param _mustStartOnOrAfter A date that the derived start must be on or come after.

  @return start The next start time.
*/
function _deriveStartFrom(JBFundingCycle memory _baseFundingCycle, uint256 _mustStartOnOrAfter) private pure returns (uint256 start) {
  // A subsequent cycle to one with a duration of 0 should start as soon as possible.
  if (_baseFundingCycle.duration == 0) return _mustStartOnOrAfter;

  // Save a reference to the cycle's duration measured in seconds.
  uint256 _cycleDurationInSeconds = _baseFundingCycle.duration * _SECONDS_IN_DAY;

  // The time when the funding cycle immediately after the specified funding cycle starts.
  uint256 _nextImmediateStart = _baseFundingCycle.start + _cycleDurationInSeconds;

  // If the next immediate start is now or in the future, return it.
  if (_nextImmediateStart >= _mustStartOnOrAfter) return _nextImmediateStart;

  // The amount of seconds since the `_mustStartOnOrAfter` time that results in a start time that might satisfy the specified constraints.
  uint256 _timeFromImmediateStartMultiple = (_mustStartOnOrAfter - _nextImmediateStart) %
    _cycleDurationInSeconds;
  
  // A reference to the first possible start timestamp.
  start = _mustStartOnOrAfter - _timeFromImmediateStartMultiple;

  // Add increments of duration as necessary to satisfy the threshold.
  while (_mustStartOnOrAfter > start) start = start + _cycleDurationInSeconds;
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
