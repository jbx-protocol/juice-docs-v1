# tapFrom

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Tap funds from a project's currently tappable funding cycle.

  @dev
  Only a project's current terminal can tap funds for its funding cycles.

  @param _projectId The ID of the project being tapped.
  @param _amount The amount being tapped.

  @return fundingCycle The tapped funding cycle.
*/
function tapFrom(uint256 _projectId, uint256 _amount)
  external
  override
  onlyController(_projectId)
  returns (JBFundingCycle memory fundingCycle)
{
  // Get a reference to the funding cycle being tapped.
  uint256 fundingCycleId = _tappableOf(_projectId);

  // Get a reference to how much has already been tapped from this funding cycle.
  uint256 _tapped = _tappedAmountOf[fundingCycleId];

  // Amount must be within what is still tappable.
  require(_amount <= _targetOf[fundingCycleId] - _tapped, 'INSUFFICIENT_FUNDS');

  // The new amount that has been tapped.
  uint256 _newTappedAmount = _tapped + _amount;

  // Store the new amount.
  _tappedAmountOf[fundingCycleId] = _newTappedAmount;

  emit Tap(fundingCycleId, _projectId, _amount, _newTappedAmount, msg.sender);

  return _getStructFor(fundingCycleId);
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

