# \_deriveWeightFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The accumulated weight change since the specified funding cycle.**

Definition:

```javascript
function _deriveWeightFrom(
  JBFundingCycle memory _baseFundingCycle,
  JBFundingCycle memory _latestPermanentFundingCycle,
  uint256 _start
) internal pure returns (uint256 weight) { ... }
```

* `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
* `_latestPermanentFundingCycle` is the latest funding cycle in the same project as `_baseFundingCycle` to not have a cycle limit.
* `_start`is a time that the cycle having a weight derived for starts.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns a weight with 18 decimal places.

1. If the base funding cycle has no duration, the derived weight should be calculated from it no matter how much time has passed since it was active.   
  
   The `discountRate` property in a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md)is out of 200. Discount rates represent a number between 0-50%, with 0.25% fidelity, so the calculation must be made out of 400.

   ```javascript
   // A subsequent cycle to one with a duration of 0 should have the next possible weight.
   if (_baseFundingCycle.duration == 0)
     return PRBMath.mulDiv(_baseFundingCycle.weight, 400 - _baseFundingCycle.discountRate, 400);
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

4. The calculations that follow will progressively apply discount rates to the `_baseFundingCycle`'s weight to arrive at the correct weight to return.

   ```javascript
   // The weight should be based off the base funding cycle's weight.
   weight = _baseFundingCycle.weight;
   ```

5. If the start time is past the limit length, the calculation must take both the limited cycle's discount into account as well as the latest permanent cycle's. Get a reference to the need for this requirement.

   ```javascript
   // If the start time is past the limit length, the calculation must take both the limited cycle's discount into account
   // as well as the latest permanent cycle's.
   bool _crossesCycleLimit = _limitLength > 0 && _limitLength <= _startDistance;
   ```

6. Apply the `_baseFundingCycle`'s discount rate, if there is one. Apply the rate as many times as there have been cycles within the `_startDistance`, or until the end of the cycle limit.  


   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

   ```javascript
   // Apply the base funding cycle's discount rate, if necessary.
   if (_baseFundingCycle.discountRate > 0) {
     uint256 _discountMultiple = _crossesCycleLimit
       ? _baseFundingCycle.cycleLimit
       : _startDistance / (_baseFundingCycle.duration * _SECONDS_IN_DAY);

     for (uint256 i = 0; i < _discountMultiple; i++)
       // The number of times to apply the discount rate.
       // Base the new weight on the specified funding cycle's weight.
       weight = PRBMath.mulDiv(weight, 400 - _baseFundingCycle.discountRate, 400);
   }
   ```

7. Apply the `_latestPermanentFundingCycle`'s discount rate, if there is one. Apply the rate as many times as there have been cycles since the end of the cycle limit.  


   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

   ```javascript
   // Apply the latest permanent funding cycle's discount rate, if necessary.
   if (_crossesCycleLimit && _latestPermanentFundingCycle.discountRate > 0) {
     // The number of times to apply the latest permanent discount rate.
     uint256 _permanentDiscountMultiple = (_startDistance - _limitLength) /
       (_latestPermanentFundingCycle.duration * _SECONDS_IN_DAY);

     for (uint256 i = 0; i < _permanentDiscountMultiple; i++)
       // base the weight on the result of the previous calculation.
       weight = PRBMath.mulDiv(weight, 400 - _latestPermanentFundingCycle.discountRate, 400);
   }
   ```



  
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  The accumulated weight change since the specified funding cycle.

  @param _baseFundingCycle The funding cycle to make the calculation with.
  @param _latestPermanentFundingCycle The latest funding cycle in the same project as `_baseFundingCycle` to not have a limit.
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
  uint256 _limitLength = _baseFundingCycle.cycleLimit == 0
    ? 0
    : _baseFundingCycle.cycleLimit * (_baseFundingCycle.duration * _SECONDS_IN_DAY);

  // The weight should be based off the base funding cycle's weight.
  weight = _baseFundingCycle.weight;

  // If the start time is past the limit length, the calculation must take both the limited cycle's discount into account
  // as well as the latest permanent cycle's.
  bool _crossesCycleLimit = _limitLength > 0 && _limitLength <= _startDistance;

  // Apply the base funding cycle's discount rate, if necessary.
  if (_baseFundingCycle.discountRate > 0) {
    uint256 _discountMultiple = _crossesCycleLimit
      ? _baseFundingCycle.cycleLimit
      : _startDistance / (_baseFundingCycle.duration * _SECONDS_IN_DAY);

    for (uint256 i = 0; i < _discountMultiple; i++)
      // The number of times to apply the discount rate.
      // Base the new weight on the specified funding cycle's weight.
      weight = PRBMath.mulDiv(weight, 400 - _baseFundingCycle.discountRate, 400);
  }

  // Apply the latest permanent funding cycle's discount rate, if necessary.
  if (_crossesCycleLimit && _latestPermanentFundingCycle.discountRate > 0) {
    // The number of times to apply the latest permanent discount rate.
    uint256 _permanentDiscountMultiple = (_startDistance - _limitLength) /
      (_latestPermanentFundingCycle.duration * _SECONDS_IN_DAY);

    for (uint256 i = 0; i < _permanentDiscountMultiple; i++)
      // base the weight on the result of the previous calculation.
      weight = PRBMath.mulDiv(weight, 400 - _latestPermanentFundingCycle.discountRate, 400);
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



