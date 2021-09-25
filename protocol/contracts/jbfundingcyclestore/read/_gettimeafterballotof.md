# \_getTimeAfterBallotOf

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice
  The time after the ballot of the provided funding cycle has expired.

  @dev
  If the ballot ends in the past, the current block timestamp will be returned.

  @param _fundingCycle The ID funding cycle to make the caluclation the ballot of.
  @param _from The time from which the ballot duration should be calculated.

  @return The time when the ballot duration ends.
*/
function _getTimeAfterBallotOf(JBFundingCycle memory _fundingCycle, uint256 _from)
  private
  view
  returns (uint256)
{
  // The ballot must have ended.
  uint256 _ballotExpiration = _fundingCycle.ballot != IJBFundingCycleBallot(address(0))
    ? _from + _fundingCycle.ballot.duration()
    : 0;

  return block.timestamp > _ballotExpiration ? block.timestamp : _ballotExpiration;
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

