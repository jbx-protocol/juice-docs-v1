# \_deriveStartFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The date that is the nearest multiple of the specified funding cycle's duration from its end.

  @param _baseFundingCycle The funding cycle to make the calculation for.
  @param _latestPermanentFundingCycle The latest funding cycle in the same project as `_baseFundingCycle` to not have a limit.
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

  // Save a reference to the duration measured in seconds.
  uint256 _durationInSeconds = _baseFundingCycle.duration * SECONDS_IN_DAY;

  // The time when the funding cycle immediately after the specified funding cycle starts.
  uint256 _nextImmediateStart = _baseFundingCycle.start + _durationInSeconds;

  // If the next immediate start is now or in the future, return it.
  if (_nextImmediateStart >= _mustStartOnOrAfter) return _nextImmediateStart;

  uint256 _cycleLimit = _baseFundingCycle.cycleLimit;

  uint256 _timeFromImmediateStartMultiple;
  // Only use base
  if (_mustStartOnOrAfter <= _baseFundingCycle.start + _durationInSeconds * _cycleLimit) {
    // Otherwise, use the closest multiple of the duration from the old end.
    _timeFromImmediateStartMultiple =
      (_mustStartOnOrAfter - _nextImmediateStart) %
      _durationInSeconds;
  } else {
    // If the cycle has ended, make the calculation with the latest permanent funding cycle.
    _timeFromImmediateStartMultiple = _latestPermanentFundingCycle.duration == 0
      ? 0
      : ((_mustStartOnOrAfter - (_baseFundingCycle.start + (_durationInSeconds * _cycleLimit))) %
        (_latestPermanentFundingCycle.duration * SECONDS_IN_DAY));

    // Use the duration of the permanent funding cycle from here on out.
    _durationInSeconds = _latestPermanentFundingCycle.duration * SECONDS_IN_DAY;
  }

    // Otherwise use an increment of the duration from the most recent start.
    start = _mustStartOnOrAfter - _timeFromImmediateStartMultiple;

    // Add increments of duration as necessary to satisfy the threshold.
    while (_mustStartOnOrAfter > start) start = start + _durationInSeconds;
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

