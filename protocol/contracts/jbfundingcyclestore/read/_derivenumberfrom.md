# \_deriveNumberFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The number of the next funding cycle given the specified funding cycle.**

Definition:

```javascript
function _deriveNumberFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _start
) private pure returns (uint256 number) { ... }
```

* `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
* `_latestPermanentFundingCycle` is the latest funding cycle in the same project as `_baseFundingCycle` to not have a cycle limit.
* `_start`is a time that the cycle having a weight derived for starts.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the funding cycle number.

1. If the `_baseFundingCycle` doesn't have a duration, the next number is 1 more than the base's number. 

   ```javascript
   // A subsequent cycle to one with a duration of 0 should be the next number.
   if (_baseFundingCycle.duration == 0) return _baseFundingCycle.number + 1;
   ```

2. Get a reference to how long after the `_baseFundingCycle`'s start the specified `_start` time is. The goal will be to see how many cycles have passed within this time distance. 

   ```javascript
   // The difference between the start of the base funding cycle and the proposed start.
   uint256 _startDistance = _start - _baseFundingCycle.start;
   ```

3. If the `_baseFundingCycle` has a cycle limit, the calculation must take that into account. Get a reference to the length of the limit in seconds.  


   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

   ```javascript
   // The number of seconds that the base funding cycle is limited to.
   uint256 _limitLength = _baseFundingCycle.cycleLimit == 0
     ? 0
     : _baseFundingCycle.cycleLimit * (_baseFundingCycle.duration * _SECONDS_IN_DAY);
   ```

4. If the start time is past the limit length, the calculation must take both the limited cycle's discount into account as well as the latest permanent cycle's. Get a reference to the need for this requirement.

   ```javascript
   // If the start time is past the limit length, the calculation must take both the limited cycle's discount into account
   // as well as the latest permanent cycle's.
   bool _crossesCycleLimit = _limitLength > 0 && _limitLength <= _startDistance;
   ```

5. Get a reference to the time distance within which the base cycle duration should be assumed. If the start distance crosses the cycle limit, use the limit length. Otherwise use the full distance.

   ```javascript
   // The time distance within which the base cycle duration should be assumed.
   uint256 _baseDistance = _crossesCycleLimit ? _limitLength : _startDistance;
   ```

6. Find the number of base cycles that fit in the base distance.  


   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

   ```javascript
   // Find the number of base cycles that fit in the base distance.
   number =
     _baseFundingCycle.number +
     (_baseDistance / (_baseFundingCycle.duration * _SECONDS_IN_DAY));
   ```

7. If needed, add the number of latest permanent cycles that fit in the time after the limit. If the latest permanent cycle doesn't have a duration, no additional cycles have passed.  


   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

   ```javascript
   // If needed, add the number of latest permanent cycles that fit in the time after the limit.
   if (_crossesCycleLimit && _latestPermanentFundingCycle.duration > 0)
     number =
       number +
       ((_startDistance - _limitLength) /
         (_latestPermanentFundingCycle.duration * _SECONDS_IN_DAY));
   ```

  

  
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The number of the next funding cycle given the specified funding cycle.

  @param _baseFundingCycle The funding cycle to make the calculation with.
  @param _latestPermanentFundingCycle The latest funding cycle in the same project as `_baseFundingCycle` to not have a limit.
  @param _start The start time to derive a number for.

  @return number The next number.
*/
function _deriveNumberFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _start
) private pure returns (uint256 number) {
  // A subsequent cycle to one with a duration of 0 should be the next number.
  if (_baseFundingCycle.duration == 0) return _baseFundingCycle.number + 1;

  // The difference between the start of the base funding cycle and the proposed start.
  uint256 _startDistance = _start - _baseFundingCycle.start;

  // The number of seconds that the base funding cycle is limited to.
  uint256 _limitLength = _baseFundingCycle.cycleLimit == 0
    ? 0
    : _baseFundingCycle.cycleLimit * (_baseFundingCycle.duration * _SECONDS_IN_DAY);

  // If the start time is past the limit length, the calculation must take both the limited cycle's discount into account
  // as well as the latest permanent cycle's.
  bool _crossesCycleLimit = _limitLength > 0 && _limitLength <= _startDistance;

  // The time distance within which the base cycle duration should be assumed.
  uint256 _baseDistance = _crossesCycleLimit ? _limitLength : _startDistance;

  // Find the number of base cycles that fit in the base distance.
  number =
    _baseFundingCycle.number +
    (_baseDistance / (_baseFundingCycle.duration * _SECONDS_IN_DAY));

  // If needed, add the number of latest permanent cycles that fit in the time after the limit.
  if (_crossesCycleLimit && _latestPermanentFundingCycle.duration > 0)
    number =
      number +
      ((_startDistance - _limitLength) /
        (_latestPermanentFundingCycle.duration * _SECONDS_IN_DAY));
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

