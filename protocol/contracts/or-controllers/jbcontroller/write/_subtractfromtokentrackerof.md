# _subtractFromTokenTrackerOf

{% tabs %}
{% tab title="Step by step" %}

**Efficiently stores a funding cycles provided configuration properties.**

# Definition

```solidity
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

* Arguments:
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

# Body
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice
  Subtracts the provided value from the processed token tracker.

  @dev
  Necessary to account for both positive and negative values.

  @param _projectId The ID of the project that is having its tracker subtracted from.
  @param _amount The amount to subtract.

*/
function _subtractFromTokenTrackerOf(uint256 _projectId, uint256 _amount) private {
  // Get a reference to the processed token tracker for the project.
  int256 _processedTokenTracker = _processedTokenTrackerOf[_projectId];

  // Subtract the count from the processed token tracker.
  // If there are at least as many processed tokens as the specified amount,
  // the processed token tracker of the project will be positive. Otherwise it will be negative.
  _processedTokenTrackerOf[_projectId] = _processedTokenTracker < 0 // If the tracker is negative, add the count and reverse it.
    ? -int256(uint256(-_processedTokenTracker) + _amount) // the tracker is less than the count, subtract it from the count and reverse it.
    : _processedTokenTracker < int256(_amount)
    ? -(int256(_amount) - _processedTokenTracker) // simply subtract otherwise.
    : _processedTokenTracker - int256(_amount);
}
```
{% endtab %}
{% endtabs %}
