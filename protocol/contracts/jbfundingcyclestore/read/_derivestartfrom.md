# \_deriveStartFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The date that is the nearest multiple of the specified funding cycle's duration from its end.**

Definition:

```javascript
function _deriveStartFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _mustStartOnOrAfter
) internal pure returns (uint256 start) { ... }
```

* `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
* `_latestPermanentFundingCycle` is the latest funding cycle in the same project as `_baseFundingCycle` to not have a cycle limit.
* `_mustStartOnOrAfter`is a date that the derived start must be on or come after.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns a timestamp in seconds.

1. A funding cycle with a duration of 0 can start as soon as possible. 

   ```javascript
   // A subsequent cycle to one with a duration of 0 should start as soon as possible.
   if (_baseFundingCycle.duration == 0) return _mustStartOnOrAfter;
   ```

2. Get a reference to the duration of the base cycle in seconds.  


   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

   ```javascript
   // Save a reference to the cycle's duration measured in seconds.
   uint256 _cycleDurationInSeconds = _baseFundingCycle.duration * _SECONDS_IN_DAY;
   ```

3. Get a reference to the start time of the cycle immediately following the base cycle. This is the base cycles start time plus the base cycle's duration.

   ```javascript
   // The time when the funding cycle immediately after the specified funding cycle starts.
   uint256 _nextImmediateStart = _baseFundingCycle.start + _baseCycleDurationInSeconds;
   ```

4. If the `_nextImmediateStart` is allowed, it should be used. Otherwise we'll have to calculate a value depending on how much time has passed since the `_nextImmediateStart`.

   ```javascript
   // If the next immediate start is now or in the future, return it.
   if (_nextImmediateStart >= _mustStartOnOrAfter) return _nextImmediateStart;
   ```

5. Save a reference to when the base funding cycle's cycle limit ends. If there is no cycle limit, use 1 to return when the current funding cycle ends.

   ```javascript
   // Find when the base cycle's cycle limit ends. No cycle limit has the same behavior of a cycle limit of 1.
   uint256 _baseFundingCycleLimitEnd = _baseFundingCycle.start +
     (_cycleDurationInSeconds *
       (_baseFundingCycle.cycleLimit == 0 ? 1 : _baseFundingCycle.cycleLimit));
   ```

6. Save a reference to a flag indicating if the start time that'll be returned kicks off a funding cycle with a configuration like the base funding cycle or like the latest permanent funding cycle. The latest permanent cycle should be used if the specified `_baseFundingCycle` and `_latestPermanentFundingCycle` are different, and the date that the start date must fall on or after is after the `_baseFundingCycle`'s cycle limit end.

   ```javascript
   // If the base funding cycle is different from the latest permanent funding cycle,
   // and the funding cycle should start after the base cycle limit,
   // the returned start time will be of an instance of the latest permanent funding cycle.
   bool _shouldRevertToLatestPermanentCycle = _baseFundingCycle.id !=
     _latestPermanentFundingCycle.id &&
     _mustStartOnOrAfter > _baseFundingCycleLimitEnd;
   ```

7. If the `_shouldRevertToLatestPermanentCycle`, use the duration from the `_latestPermanentFundingCycle` rather than the base.

   ```javascript
   // Use the duration of the permanent funding cycle as the base if needed.
   _cycleDurationInSeconds = _shouldRevertToLatestPermanentCycle
     ? _latestPermanentFundingCycle.duration * _SECONDS_IN_DAY
     : _cycleDurationInSeconds;
   ```

8. Save a reference to the amount of seconds since the `_mustStartOnOrAfter` time that results in a start time that might satisfy the specified constraints.

   ```javascript
   // The amount of seconds since the `_mustStartOnOrAfter` time that results in a start time that might satisfy the specified constraints.
   uint256 _timeFromImmediateStartMultiple = (_mustStartOnOrAfter -
     (_shouldRevertToLatestPermanentCycle ? _baseFundingCycleLimitEnd : _nextImmediateStart)) %
     _cycleDurationInSeconds;
   ```

9. Save a reference to the first possible start time.

   ```javascript
   // A reference to the first possible start timestamp.
   start = _mustStartOnOrAfter - _timeFromImmediateStartMultiple;
   ```

10. It's possible that the `start` time doesn't satisfy the specified constraints. If so, add increments of the funding cycle's duration as necessary to satisfy the threshold.

    ```javascript
      // Add increments of duration as necessary to satisfy the threshold.
      while (_mustStartOnOrAfter > start) start = start + _cycleDurationInSeconds;
    ```

  
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The date that is the nearest multiple of the specified funding cycle's duration from its end.

  @param _baseFundingCycle The funding cycle to make the calculation for.
  @param _latestPermanentFundingCycle The latest funding cycle in the same project as `_baseFundingCycle` to not have a cycle limit.
  @param _mustStartOnOrAfter A date that the derived start must be on or come after.

  @return start The next start time.
*/
function _deriveStartFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _mustStartOnOrAfter
) internal pure returns (uint256 start) {
  // A subsequent cycle to one with a duration of 0 should start as soon as possible.
  if (_baseFundingCycle.duration == 0) return _mustStartOnOrAfter;

  // Save a reference to the cycle's duration measured in seconds.
  uint256 _cycleDurationInSeconds = _baseFundingCycle.duration * _SECONDS_IN_DAY;

  // The time when the funding cycle immediately after the specified funding cycle starts.
  uint256 _nextImmediateStart = _baseFundingCycle.start + _cycleDurationInSeconds;

  // If the next immediate start is now or in the future, return it.
  if (_nextImmediateStart >= _mustStartOnOrAfter) return _nextImmediateStart;

  // Find when the base cycle's cycle limit ends. No cycle limit has the same behavior of a cycle limit of 1.
  uint256 _baseFundingCycleLimitEnd = _baseFundingCycle.start +
    (_cycleDurationInSeconds *
      (_baseFundingCycle.cycleLimit == 0 ? 1 : _baseFundingCycle.cycleLimit));

  // If the base funding cycle is different from the latest permanent funding cycle,
  // and the funding cycle should start after the base cycle limit,
  // the returned start time will be of an instance of the latest permanent funding cycle.
  bool _shouldRevertToLatestPermanentCycle = _baseFundingCycle.id !=
    _latestPermanentFundingCycle.id &&
    _mustStartOnOrAfter > _baseFundingCycleLimitEnd;

  // Use the duration of the permanent funding cycle as the base if needed.
  _cycleDurationInSeconds = _shouldRevertToLatestPermanentCycle
    ? _latestPermanentFundingCycle.duration * _SECONDS_IN_DAY
    : _cycleDurationInSeconds;

  // The amount of seconds since the `_mustStartOnOrAfter` time that results in a start time that might satisfy the specified constraints.
  uint256 _timeFromImmediateStartMultiple = (_mustStartOnOrAfter -
    (_shouldRevertToLatestPermanentCycle ? _baseFundingCycleLimitEnd : _nextImmediateStart)) %
    _cycleDurationInSeconds;
  
  // A reference to the first possible start timestamp.
  start = _mustStartOnOrAfter - _timeFromImmediateStartMultiple;

  // Add increments of duration as necessary to satisfy the threshold.
  while (_mustStartOnOrAfter > start) start = start + _cycleDurationInSeconds;
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}

