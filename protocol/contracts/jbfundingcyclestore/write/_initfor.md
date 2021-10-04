# \_initFor

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Initializes a funding cycle with the appropriate properties.

  @param _projectId The ID of the project to which the funding cycle being initialized belongs.
  @param _baseFundingCycle The funding cycle to base the initialized one on.
  @param _mustStartOnOrAfter The time before which the initialized funding cycle can't start.
  @param _copy If non-intrinsic properties should be copied from the base funding cycle.

  @return newFundingCycleId The ID of the initialized funding cycle.
*/
function _initFor(
  uint256 _projectId,
  JBFundingCycle memory _baseFundingCycle,
  uint256 _mustStartOnOrAfter,
  uint256 _weight,
  bool _copy
) private returns (uint256 newFundingCycleId) {
  uint256 _id;

  // If there is no base, initialize a first cycle.
  if (_baseFundingCycle.id == 0) {
    // The first number is 1.
    uint256 _number = 1;

    // Get the formatted ID.
    _id = _idFor(_projectId, _number);

    // Set fresh intrinsic properties.
    _packAndStoreIntrinsicPropertiesOf(
      _projectId,
      _number,
      _weight,
      _baseFundingCycle.id,
      block.timestamp
    );
  } else {
    // Update the intrinsic properties of the funding cycle being initialized.
    _id = _updateFundingCycleBasedOn(_baseFundingCycle, _mustStartOnOrAfter, _weight, _copy);
  }

  // Set the project's latest funding cycle ID to the new count.
  latestIdOf[_projectId] = _id;

  // Get a reference to the funding cycle with updated intrinsic properties.
  JBFundingCycle memory _fundingCycle = _getStructFor(_id);

  emit Init(
    _id,
    _fundingCycle.projectId,
    _fundingCycle.number,
    _fundingCycle.basedOn,
    _fundingCycle.weight,
    _fundingCycle.start
  );

  return _fundingCycle.id;
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



