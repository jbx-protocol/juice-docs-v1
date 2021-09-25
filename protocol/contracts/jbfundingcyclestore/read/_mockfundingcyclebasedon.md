# \_mockFundingCycleBasedOn

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  A view of the funding cycle that would be created based on the provided one if the project doesn't make a reconfiguration.

  @param _baseFundingCycle The funding cycle to make the calculation for.
  @param _allowMidCycle Allow the mocked funding cycle to already be mid cycle.

  @return The next funding cycle, with an ID set to 0.
*/
function _mockFundingCycleBasedOn(JBFundingCycle memory _baseFundingCycle, bool _allowMidCycle)
  internal
  view
  returns (JBFundingCycle memory)
{
  // Can't mock a non recurring funding cycle.
  if (_baseFundingCycle.discountRate == 201) return _getStructFor(0);

  // If the base has a limit, find the last permanent funding cycle, which is needed to make subsequent calculations.
  // Otherwise, the base is already the latest permanent funding cycle.
  JBFundingCycle memory _latestPermanentFundingCycle = _baseFundingCycle.cycleLimit > 0
    ? _latestPermanentCycleBefore(_baseFundingCycle)
    : _baseFundingCycle;

  // The distance of the current time to the start of the next possible funding cycle.
  uint256 _timeFromImmediateStartMultiple;

  if (_allowMidCycle && _baseFundingCycle.duration > 0) {
    // Get the end time of the last cycle.
    uint256 _cycleEnd = _baseFundingCycle.start +
      (_baseFundingCycle.cycleLimit * _baseFundingCycle.duration * SECONDS_IN_DAY);

    // If the cycle end time is in the past, the mock should start at a multiple of the last permanent cycle since the cycle ended.
    if (_baseFundingCycle.cycleLimit > 0 && _cycleEnd < block.timestamp) {
      _timeFromImmediateStartMultiple = _latestPermanentFundingCycle.duration == 0
        ? 0
        : ((block.timestamp - _cycleEnd) %
          (_latestPermanentFundingCycle.duration * SECONDS_IN_DAY));
    } else {
      _timeFromImmediateStartMultiple = _baseFundingCycle.duration * SECONDS_IN_DAY;
    }
  } else {
    _timeFromImmediateStartMultiple = 0;
  }

  // Derive what the start time should be.
  uint256 _start = _deriveStartFrom(
    _baseFundingCycle,
    _latestPermanentFundingCycle,
    block.timestamp - _timeFromImmediateStartMultiple
  );

  // Derive what the cycle limit should be.
  uint256 _cycleLimit = _deriveCycleLimitFrom(_baseFundingCycle, _start);

  // Copy the last permanent funding cycle if the bases' limit is up.
  JBFundingCycle memory _fundingCycleToCopy = _cycleLimit == 0
    ? _latestPermanentFundingCycle
    : _baseFundingCycle;

  return
    JBFundingCycle(
      0,
      _fundingCycleToCopy.projectId,
      _deriveNumberFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start),
      _fundingCycleToCopy.id,
      _fundingCycleToCopy.configured,
      _cycleLimit,
      _deriveWeightFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start),
      _fundingCycleToCopy.ballot,
      _start,
      _fundingCycleToCopy.duration,
      _fundingCycleToCopy.target,
      _fundingCycleToCopy.currency,
      _fundingCycleToCopy.fee,
      _fundingCycleToCopy.discountRate,
      0,
      _fundingCycleToCopy.metadata
    );
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

