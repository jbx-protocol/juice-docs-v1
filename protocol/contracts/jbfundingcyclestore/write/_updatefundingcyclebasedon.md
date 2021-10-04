# \_updateFundingCycleBasedOn

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice
  Updates intrinsic properties for a funding cycle given a base cycle.

  @param _baseFundingCycle The cycle that the one being updated is based on.
  @param _mustStartOnOrAfter The time before which the initialized funding cycle can't start.
  @param _copy If non-intrinsic properties should be copied from the base funding cycle.

  @return fundingCycleId The ID of the funding cycle that was updated.
*/
function _updateFundingCycleBasedOn(
  JBFundingCycle memory _baseFundingCycle,
  uint256 _mustStartOnOrAfter,
  uint256 _weight,
  bool _copy
) private returns (uint256 fundingCycleId) {
  // Get the latest permanent funding cycle.
  JBFundingCycle memory _latestPermanentFundingCycle = _baseFundingCycle.cycleLimit > 0
    ? _latestPermanentCycleFrom(_baseFundingCycle)
    : _baseFundingCycle;

  // Derive the correct next start time from the base.
  uint256 _start = _deriveStartFrom(
    _baseFundingCycle,
    _latestPermanentFundingCycle,
    _mustStartOnOrAfter
  );

  // A weight of 1 is treated as a weight of 0.
  _weight = _weight > 0
    ? (_weight == 1 ? 0 : _weight)
    : _deriveWeightFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start);

  // Derive the correct number.
  uint256 _number = _deriveNumberFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start);

  // Update the intrinsic properties.
  fundingCycleId = _packAndStoreIntrinsicPropertiesOf(
    _baseFundingCycle.projectId,
    _number,
    _weight,
    _baseFundingCycle.id,
    _start
  );

  // Copy if needed.
  if (_copy) {
    // Derive what the cycle limit should be.
    uint256 _cycleLimit = _deriveCycleLimitFrom(_baseFundingCycle, _start);

    // Copy the last permanent funding cycle if the bases' limit is up.
    JBFundingCycle memory _fundingCycleToCopy = _cycleLimit == 0
      ? _latestPermanentFundingCycle
      : _baseFundingCycle;

    // Save the configuration efficiently.
    _packAndStoreConfigurationPropertiesOf(
      fundingCycleId,
      _fundingCycleToCopy.configured,
      _cycleLimit,
      _fundingCycleToCopy.ballot,
      _fundingCycleToCopy.duration,
      _fundingCycleToCopy.currency,
      _fundingCycleToCopy.fee,
      _fundingCycleToCopy.discountRate
    );

    _metadataOf[fundingCycleId] = _metadataOf[_fundingCycleToCopy.id];
    _targetOf[fundingCycleId] = _targetOf[_fundingCycleToCopy.id];
  }
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

