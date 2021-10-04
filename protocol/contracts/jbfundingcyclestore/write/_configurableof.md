# \_configurableOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Returns the configurable funding cycle for this project if it exists, otherwise creates one.**  
  
Definition:

```javascript
function _configurableOf(
  uint256 _projectId,
  uint256 _configured,
  uint256 _weight,
  bool _configureActiveFundingCycle
) private returns (uint256 fundingCycleId) { ... }
```

* `_projectId` is the ID of the project to find a configurable funding cycle for.
* `_configured` is the time at which the configuration is occurring.
* `_weight` is the weight to store along with a newly created configurable funding cycle.
* `_configureActiveFundingCycles` is if the active funding cycle should be configurable. Otherwise the next funding cycle will be used.
* The function is private to this contract.
* The function returns the ID of a configurable funding cycle.
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Returns the configurable funding cycle for this project if it exists, otherwise creates one.

  @param _projectId The ID of the project to find a configurable funding cycle for.
  @param _configured The time at which the configuration is occurring.
  @param _weight The weight to store along with a newly created configurable funding cycle.
  @param _configureActiveFundingCycle If the active funding cycle should be configurable. Otherwise the next funding cycle will be used.

  @return fundingCycleId The ID of the configurable funding cycle.
*/
function _configurableOf(
  uint256 _projectId,
  uint256 _configured,
  uint256 _weight,
  bool _configureActiveFundingCycle
) private returns (uint256 fundingCycleId) {
  // If there's not yet a funding cycle for the project, return the ID of a newly created one.
  if (latestIdOf[_projectId] == 0)
    return _initFor(_projectId, _getStructFor(0), block.timestamp, _weight, false);

  // Get the standby funding cycle's ID.
  fundingCycleId = _standbyOf(_projectId);

  // If it exists, make sure its updated, then return it.
  if (fundingCycleId > 0) {
    // Get the funding cycle that the specified one is based on.
    JBFundingCycle memory _baseFundingCycle = _getStructFor(
      _getStructFor(fundingCycleId).basedOn
    );

    // The base's ballot must have ended.
    _updateFundingCycleBasedOn(
      _baseFundingCycle,
      _getLatestTimeAfterBallotOf(_baseFundingCycle, _configured),
      _weight,
      false
    );
    return fundingCycleId;
  }

  // Get the active funding cycle's ID.
  fundingCycleId = _eligibleOf(_projectId);

  // If the ID of an eligible funding cycle exists, it's approved, and active funding cycles are configurable, return it.
  if (fundingCycleId > 0) {
    if (!_isIdApproved(fundingCycleId)) {
      // If it hasn't been approved, set the ID to be the based funding cycle,
      // which carries the last approved configuration.
      fundingCycleId = _getStructFor(fundingCycleId).basedOn;
    } else if (_configureActiveFundingCycle) {
      return fundingCycleId;
    }
  } else {
    // Get the ID of the latest funding cycle which has the latest reconfiguration.
    fundingCycleId = latestIdOf[_projectId];
  }

  // Determine if the configurable funding cycle can only take effect on or after a certain date.
  uint256 _mustStartOnOrAfter;

  // Base off of the active funding cycle if it exists.
  JBFundingCycle memory _fundingCycle = _getStructFor(fundingCycleId);

  // Make sure the funding cycle is recurring.
  require(_fundingCycle.discountRate < 201, '0x1d: NON_RECURRING');

  if (_configureActiveFundingCycle) {
    // If the duration is zero, always go back to the original start.
    if (_fundingCycle.duration == 0) {
      _mustStartOnOrAfter = _fundingCycle.start;
    } else {
      // Set to the start time of the current active start time.
      uint256 _timeFromStartMultiple = (block.timestamp - _fundingCycle.start) %
        (_fundingCycle.duration * _SECONDS_IN_DAY);
      _mustStartOnOrAfter = block.timestamp - _timeFromStartMultiple;
    }
  } else {
    // The ballot must have ended.
    _mustStartOnOrAfter = _getLatestTimeAfterBallotOf(_fundingCycle, _configured);
  }

  // Return the newly initialized configurable funding cycle.
  fundingCycleId = _initFor(_projectId, _fundingCycle, _mustStartOnOrAfter, _weight, false);
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`0x1d: NON_RECURRING`** | Thrown if a configurable funding cycle is being looked for but the base is non-recurring. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}

