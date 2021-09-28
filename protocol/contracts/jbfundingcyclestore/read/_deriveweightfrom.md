# \_deriveWeightFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The accumulated weight change since the specified funding cycle.

  @param _baseFundingCycle The funding cycle to make the calculation with.
  @param _latestPermanentFundingCycle The latest funding cycle in the same project as `_fundingCycle` to not have a limit.
  @param _start The start time to derive a weight for.

  @return weight The next weight.
*/
function _deriveWeightFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _start
) internal pure returns (uint256 weight) {
  // A subsequent cycle to one with a duration of 0 should have the next possible weight.
  if (_baseFundingCycle.duration == 0)
    return PRBMath.mulDiv(_baseFundingCycle.weight, 400 - _baseFundingCycle.discountRate, 400);

  // The difference between the start of the base funding cycle and the proposed start.
  uint256 _startDistance = _start - _baseFundingCycle.start;

  // The number of seconds that the base funding cycle is limited to.
  uint256 _limitLength = _baseFundingCycle.cycleLimit == 0 || _baseFundingCycle.basedOn == 0
    ? 0
    : _baseFundingCycle.cycleLimit * (_baseFundingCycle.duration * SECONDS_IN_DAY);

  // The weight should be based off the base funding cycle's weight.
  weight = _baseFundingCycle.weight;

  // If there's no limit or if the limit is greater than the start distance,
  // apply the discount rate of the base.
  if (_limitLength == 0 || _limitLength > _startDistance) {
    // If the discount rate is 0, return the same weight.
    if (_baseFundingCycle.discountRate == 0) return weight;

    uint256 _discountMultiple = _startDistance / (_baseFundingCycle.duration * SECONDS_IN_DAY);

    for (uint256 i = 0; i < _discountMultiple; i++) {
      // The number of times to apply the discount rate.
      // Base the new weight on the specified funding cycle's weight.
      weight = PRBMath.mulDiv(weight, 400 - _baseFundingCycle.discountRate, 400);
    }
  } else {
    // If the time between the base start at the given start is longer than
    // the limit, the discount rate for the limited base has to be applied first,
    // and then the discount rate for the last permanent should be applied to
    // the remaining distance.

    // Use up the limited discount rate up until the limit.
    if (_baseFundingCycle.discountRate > 0) {
      for (uint256 i = 0; i < _baseFundingCycle.cycleLimit; i++) {
        weight = PRBMath.mulDiv(weight, 400 - _baseFundingCycle.discountRate, 400);
      }
    }

    if (_latestPermanentFundingCycle.discountRate > 0) {
      // The number of times to apply the latest permanent discount rate.

      uint256 _permanentDiscountMultiple = _latestPermanentFundingCycle.duration == 0
        ? 0
        : (_startDistance - _limitLength) /
          (_latestPermanentFundingCycle.duration * SECONDS_IN_DAY);

      for (uint256 i = 0; i < _permanentDiscountMultiple; i++) {
        // base the weight on the result of the previous calculation.
        weight = PRBMath.mulDiv(weight, 400 - _latestPermanentFundingCycle.discountRate, 400);
      }
    }
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

