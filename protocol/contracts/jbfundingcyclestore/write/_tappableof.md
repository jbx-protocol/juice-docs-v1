# \_tappableOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Returns the funding cycle that can be tapped at the time of the call.**  
  
Definition:

```javascript
function _tappableOf(uint256 _projectId) private returns (uint256 fundingCycleId) { ... }
```

* `_projectId` is the ID of the project to find a tappable funding cycle for.
* The function is private to this contract.
* The function returns the ID of a tappable funding cycle.
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Returns the funding cycle that can be tapped at the time of the call.

  @param _projectId The ID of the project to find a tappable funding cycle for.

  @return fundingCycleId The ID of the tappable funding cycle.
*/
function _tappableOf(uint256 _projectId) private returns (uint256 fundingCycleId) {
  // Check for the ID of an eligible funding cycle.
  fundingCycleId = _eligibleOf(_projectId);

  // No eligible funding cycle found, check for the ID of a standby funding cycle.
  // If this one exists, it will become eligible one it has started.
  if (fundingCycleId == 0) fundingCycleId = _standbyOf(_projectId);

  // Keep a reference to the funding cycle eligible for being tappable.
  JBFundingCycle memory _fundingCycle;

  // If the ID of an eligible funding cycle exists,
  // check to see if it has been approved by the based funding cycle's ballot.
  if (fundingCycleId > 0) {
    // Get the necessary properties for the funding cycle.
    _fundingCycle = _getStructFor(fundingCycleId);

    // Check to see if the cycle is approved. If so, return it.
    if (_fundingCycle.start <= block.timestamp && _isApproved(_fundingCycle))
      return fundingCycleId;

    // If it hasn't been approved, set the ID to be the base funding cycle,
    // which carries the last approved configuration.
    fundingCycleId = _fundingCycle.basedOn;
  } else {
    // No upcoming funding cycle found that is eligible to become active, clone the latest active funding cycle.
    // which carries the last configuration.
    fundingCycleId = latestIdOf[_projectId];

    // Get the funding cycle for the latest ID.
    _fundingCycle = _getStructFor(fundingCycleId);

    // If it's not approved, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
    if (!_isApproved(_fundingCycle)) fundingCycleId = _fundingCycle.basedOn;
  }

  // The funding cycle cant be 0.
  require(fundingCycleId > 0, '0x1e: NOT_FOUND');

  // Set the eligible funding cycle.
  _fundingCycle = _getStructFor(fundingCycleId);

  // Funding cycles with a discount rate of 100% are non-recurring.
  require(_fundingCycle.discountRate < 201, '0x1f: NON_RECURRING');

  // The time when the funding cycle immediately after the eligible funding cycle starts.
  uint256 _nextImmediateStart = _fundingCycle.start + (_fundingCycle.duration * SECONDS_IN_DAY);

  // The distance from now until the nearest past multiple of the cycle duration from its start.
  // A duration of zero means the reconfiguration can start right away.
  uint256 _timeFromImmediateStartMultiple = _fundingCycle.duration == 0
    ? 0
    : (block.timestamp - _nextImmediateStart) % (_fundingCycle.duration * SECONDS_IN_DAY);

  // Return the tappable funding cycle.
  fundingCycleId = _initFor(
    _projectId,
    _fundingCycle,
    block.timestamp - _timeFromImmediateStartMultiple,
    0,
    true
  );
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`0x1d: NOT_FOUND`** | Thrown if no tappable funding cycle was found. |
| **`0x1d: NON_RECURRING`** | Thrown if a tappable funding cycle is being looked for but the base is non-recurring. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}



