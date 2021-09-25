# \_ballotStateOf

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  A funding cycle configuration's currency status.

  @param _id The ID of the funding cycle configuration to check the status of.
  @param _configuration The timestamp of when the configuration took place.
  @param _ballotFundingCycleId The ID of the funding cycle which is configured with the ballot that should be used.

  @return The funding cycle's configuration status.
*/
function _ballotStateOf(
  uint256 _id,
  uint256 _configuration,
  uint256 _ballotFundingCycleId
) private view returns (JBBallotState) {
  // If there is no ballot funding cycle, auto approve.
  if (_ballotFundingCycleId == 0) return JBBallotState.Approved;

  // Get the ballot funding cycle.
  JBFundingCycle memory _ballotFundingCycle = _getStructFor(_ballotFundingCycleId);

  // If the configuration is the same as the ballot's funding cycle,
  // the ballot isn't applicable. Auto approve since the ballot funding cycle is approved.
  if (_ballotFundingCycle.configured == _configuration) return JBBallotState.Approved;

  // If there is no ballot, the ID is auto approved.
  // Otherwise, return the ballot's state.
  return
    _ballotFundingCycle.ballot == IJBFundingCycleBallot(address(0))
      ? JBBallotState.Approved
      : _ballotFundingCycle.ballot.state(_id, _configuration);
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

