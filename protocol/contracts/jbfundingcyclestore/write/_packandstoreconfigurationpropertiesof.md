# \_packAndStoreConfigurationPropertiesOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Efficiently stores a funding cycles provided configuration properties.**

Definition:

```javascript
function _packAndStoreConfigurationProperties(
  uint256 _fundingCycleId,
  uint256 _configured,
  uint256 _cycleLimit,
  IJBFundingCycleBallot _ballot,
  uint256 _duration,
  uint256 _currency,
  uint256 _fee,
  uint256 _discountRate
) private { ... }
```

* `_fundingCycleId` is the ID of the funding cycle to pack and store.
* `_configured` is the timestamp of the configuration.
* `_cycleLimit` is the number of cycles that this configuration should last for before going back to the last permanent.
* `_ballot` is the ballot to use for future reconfiguration approvals. 
* `_duration` is the duration of the funding cycle.
* `_currency` is the currency of the funding cycle.
* `_fee` is the fee of the funding cycle.
* `_discountRate` is the discount rate of the base funding cycle.
* The function is private to this contract.
* The function doesn't return anything.
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Efficiently stores a funding cycles provided configuration properties.

  @param _fundingCycleId The ID of the funding cycle to pack and store.
  @param _configured The timestamp of the configuration.
  @param _cycleLimit The number of cycles that this configuration should last for before going back to the last permanent.
  @param _ballot The ballot to use for future reconfiguration approvals. 
  @param _duration The duration of the funding cycle.
  @param _currency The currency of the funding cycle.
  @param _fee The fee of the funding cycle.
  @param _discountRate The discount rate of the base funding cycle.
*/
function _packAndStoreConfigurationProperties(
  uint256 _fundingCycleId,
  uint256 _configured,
  uint256 _cycleLimit,
  IJBFundingCycleBallot _ballot,
  uint256 _duration,
  uint256 _currency,
  uint256 _fee,
  uint256 _discountRate
) private {
  // ballot in bytes 0-159 bytes.
  uint256 packed = uint160(address(_ballot));
  // configured in bytes 160-207 bytes.
  packed |= _configured << 160;
  // duration in bytes 208-223 bytes.
  packed |= _duration << 208;
  // basedOn in bytes 224-231 bytes.
  packed |= _currency << 224;
  // fee in bytes 232-239 bytes.
  packed |= _fee << 232;
  // discountRate in bytes 240-247 bytes.
  packed |= _discountRate << 240;
  // cycleLimit in bytes 248-255 bytes.
  packed |= _cycleLimit << 248;

  // Set in storage.
  _packedConfigurationPropertiesOf[_fundingCycleId] = packed;
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

