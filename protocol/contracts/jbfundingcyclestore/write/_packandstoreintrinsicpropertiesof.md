# \_packAndStoreIntrinsicPropertiesOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Efficiently stores a funding cycle's provided intrinsic properties.

  @param _projectId The ID of the project to which the funding cycle belongs.
  @param _number The number of the funding cycle.
  @param _weight The weight of the funding cycle.
  @param _basedOn The ID of the based funding cycle.
  @param _start The start time of this funding cycle.

  @return fundingCycleId The ID of the funding cycle that was updated.
*/
function _packAndStoreIntrinsicPropertiesOf(
  uint256 _projectId,
  uint256 _number,
  uint256 _weight,
  uint256 _basedOn,
  uint256 _start
) private returns (uint256 fundingCycleId) {
  // weight in bytes 0-79 bytes.
  uint256 packed = _weight;
  // projectId in bytes 80-135 bytes.
  packed |= _projectId << 80;
  // basedOn in bytes 136-183 bytes.
  packed |= _basedOn << 136;
  // start in bytes 184-231 bytes.
  packed |= _start << 184;
  // number in bytes 232-255 bytes.
  packed |= _number << 232;

  // Construct the ID.
  fundingCycleId = _idFor(_projectId, _number);

  // Set in storage.
  _packedIntrinsicPropertiesOf[fundingCycleId] = packed;
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

