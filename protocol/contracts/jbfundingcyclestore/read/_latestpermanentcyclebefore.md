# \_latestPermanentCycleBefore

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Finds the last funding cycle that was permanent in relation to the specified funding cycle.

  @dev
  Determined what the latest funding cycle with a `cycleLimit` of 0 is, or isn't based on any previous funding cycle.

  @param _fundingCycle The funding cycle to find the most recent permanent cycle compared to.

  @return fundingCycle The most recent permanent funding cycle.
*/
function _latestPermanentCycleBefore(JBFundingCycle memory _fundingCycle)
  private
  view
  returns (JBFundingCycle memory fundingCycle)
{
  if (_fundingCycle.basedOn == 0) return _fundingCycle;
  fundingCycle = _getStructFor(_fundingCycle.basedOn);
  if (fundingCycle.cycleLimit == 0) return fundingCycle;
  return _latestPermanentCycleBefore(fundingCycle);
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

