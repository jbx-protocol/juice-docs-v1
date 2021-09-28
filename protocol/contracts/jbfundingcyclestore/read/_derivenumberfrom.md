# \_deriveNumberFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The number of the next funding cycle given the specified funding cycle.

  @param _baseFundingCycle The funding cycle to make the calculation with.
  @param _latestPermanentFundingCycle The latest funding cycle in the same project as `_fundingCycle` to not have a limit.
  @param _start The start time to derive a number for.

  @return number The next number.
*/
function _deriveNumberFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _start
) internal pure returns (uint256 number) {
  // A subsequent cycle to one with a duration of 0 should be the next number.
  if (_baseFundingCycle.duration == 0) return _baseFundingCycle.number + 1;

  // The difference between the start of the base funding cycle and the proposed start.
  uint256 _startDistance = _start - _baseFundingCycle.start;

  // The number of seconds that the base funding cycle is limited to.
  uint256 _limitLength = _baseFundingCycle.cycleLimit == 0
    ? 0
    : _baseFundingCycle.cycleLimit * (_baseFundingCycle.duration * SECONDS_IN_DAY);

  if (_limitLength == 0 || _limitLength > _startDistance) {
    // If there's no limit or if the limit is greater than the start distance,
    // get the result by finding the number of base cycles that fit in the start distance.
    number =
      _baseFundingCycle.number +
      (_startDistance / (_baseFundingCycle.duration * SECONDS_IN_DAY));
  } else {
    // If the time between the base start at the given start is longer than
    // the limit, first calculate the number of cycles that passed under the limit,
    // and add any cycles that have passed of the latest permanent funding cycle afterwards.

    number =
      _baseFundingCycle.number +
      (_limitLength / (_baseFundingCycle.duration * SECONDS_IN_DAY));

    number =
      number +
      (
        _latestPermanentFundingCycle.duration == 0
          ? 0
          : ((_startDistance - _limitLength) /
            (_latestPermanentFundingCycle.duration * SECONDS_IN_DAY))
      );
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

