# \_deriveCycleLimitFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The limited number of times a funding cycle configuration can be active given the specified funding cycle.

  @param _fundingCycle The funding cycle to make the calculation with.
  @param _start The start time to derive cycles remaining for.

  @return start The inclusive nunmber of cycles remaining.
*/
function _deriveCycleLimitFrom(JBFundingCycle memory _fundingCycle, uint256 _start)
  internal
  pure
  returns (uint256)
{
  if (_fundingCycle.cycleLimit <= 1 || _fundingCycle.duration == 0) return 0;
  uint256 _cycles = ((_start - _fundingCycle.start) / (_fundingCycle.duration * SECONDS_IN_DAY));

  if (_cycles >= _fundingCycle.cycleLimit) return 0;
  return _fundingCycle.cycleLimit - _cycles;
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

