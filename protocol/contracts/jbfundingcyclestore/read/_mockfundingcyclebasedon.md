# \_mockFundingCycleBasedOn

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**A view of the funding cycle that would be created based on the provided one if the project doesn't approve a reconfiguration ahead of it starting.**  
  
_Returns an empty funding cycle if there can't be a mock funding cycle based on the provided one._

Definition:

```javascript
function _mockFundingCycleBasedOn(JBFundingCycle memory _baseFundingCycle, bool _allowMidCycle)
  internal
  view
  returns (JBFundingCycle memory) { ... }
```

* `_baseFundingCycle` is the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) that the resulting funding cycle should follow.
* `_allowMidCycle` is a flag indicating if the mocked funding cycle is allowed to already be mid cycle.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns a mock [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) of what the next funding cycle will be.

1. A funding cycle with a `discountRate` of 201 is a non-recurring funding cycle. An empty funding cycle should be returned if the base is non-recurring since there can't be subsequent cycles.  


   _Internal references:_

   * [`_getStructFor`](_getstructfor.md)

   ```javascript
   // Can't mock a non recurring funding cycle.
   if (_baseFundingCycle.discountRate == 201) return _getStructFor(0);
   ```

2. It is possible that the provided `_baseFundingCycle` has a cycle limit and should no longer be used to base a subsequent funding cycle on. If this is the case, get a reference to the `_latestPermanentFundingCycle`.  


   _Internal references:_

   * [`_latestPermanentCycleBefore`](_latestpermanentcyclebefore.md)

   ```javascript
   // If the base has a limit, find the last permanent funding cycle, which is needed to make subsequent calculations.
   // Otherwise, the base is already the latest permanent funding cycle.
   JBFundingCycle memory _latestPermanentFundingCycle = _baseFundingCycle.cycleLimit == 0
     ? _baseFundingCycle
     : _latestPermanentCycleBefore(_baseFundingCycle);
   ```

3. Save a reference to the amount of seconds since right now that the returned funding cycle could have started at.

   ```javascript
   // The distance of the current time to the start of the next possible funding cycle.
   uint256 _timeFromImmediateStartMultiple;
   ```

4. This next chunk is a little involved. The point of this is to find the number of seconds before the current time when the mock funding cycle must start on or after. There are a few possibilities.  
   1. If the call to the function does not `_allowMidCycle`, the start date must be now or in the future. This is also the case if the base funding cycle doesn't have a duration because the next funding cycle can start immediately. 
   2. If neither of these cases apply, it's necessary to check if the provided `_baseFundingCycle` has a cycle limit. If it does not, moving back one full duration period of the `_baseFundingCycle` will find the most recent possible start time for the mock cycle to start. 
   3. If there is a cycle limit, find the time when the cycle ends.  
  
      If the cycle has already ended, the nearest start time possible is the cycle's end. Otherwise, any cycle starting after one full cycle's duration ago will be correct.   


      _Internal references:_

      * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)

      ```javascript
      // If the returned mock cycle must not yet have started, the start time of the mock must be in the future so no need to adjust backwards.
      // If the base funding cycle doesn't have a duration, no adjustment is necessary because the next cycle can start immediately.
      if (!_allowMidCycle || _baseFundingCycle.duration == 0) {
        _timeFromImmediateStartMultiple = 0;
      } else if (_baseFundingCycle.cycleLimit == 0) {
        _timeFromImmediateStartMultiple = _baseFundingCycle.duration * _SECONDS_IN_DAY;
      } else {
       // Get the end time of the last cycle.
        uint256 _cycleEnd = _baseFundingCycle.start +
          (_baseFundingCycle.cycleLimit * _baseFundingCycle.duration * _SECONDS_IN_DAY);

        // If the cycle end time is in the past, the mock should start at a multiple of the last permanent cycle since the cycle ended.
        _timeFromImmediateStartMultiple = _cycleEnd < block.timestamp
          ? block.timestamp - _cycleEnd
          : _baseFundingCycle.duration * _SECONDS_IN_DAY;
      }
      ```

   4. Find the correct start time for the mock funding cycle.  


      _Internal references:_

      * [`_deriveStartFrom`](_derivestartfrom.md)

      ```javascript
      // Derive what the start time should be.
      uint256 _start = _deriveStartFrom(
        _baseFundingCycle,
        _latestPermanentFundingCycle,
        block.timestamp - _timeFromImmediateStartMultiple
      );
      ```

   5. Find the correct cycle limit for the mock funding cycle.   


      _Internal references:_

      * [`_deriveCycleLimitFrom`](_derivecyclelimitfrom.md)

      ```javascript
      // Derive what the cycle limit should be.
      uint256 _cycleLimit = _deriveCycleLimitFrom(_baseFundingCycle, _start);
      ```

   6. Find the correct number for the mock funding cycle.  


      _Internal references:_

      * [`_deriveNumberFrom`](_derivenumberfrom.md)

      ```javascript
      // Derive what the number should be.
      uint256 _number = _deriveNumberFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start);
      ```

   7. Get a reference to the funding cycle that should be copied. If the cycle limit is 0, copy the `_latestPermanentFundingCycle`, which is the same cycle as the `_baseFundingCycle` there the specified base doesn't have a cycleLimit \(2\). Otherwise use the `_baseFundingCycle` that was provided.   

      ```javascript
      // Copy the last permanent funding cycle if the bases' limit is up.
      JBFundingCycle memory _fundingCycleToCopy = _cycleLimit == 0
        ? _latestPermanentFundingCycle
        : _baseFundingCycle;
      ```

   8. Return a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) with the aggregated configuration.  


      _Internal references:_

      * [`_idFor`](_idfor.md)
      * [`_deriveWeightFrom`](_deriveweightfrom.md)

      ```javascript
      return
        JBFundingCycle(
          _idFor(_fundingCycleToCopy.projectId, _number),
          _fundingCycleToCopy.projectId,
          _number,
          _fundingCycleToCopy.id,
          _fundingCycleToCopy.configured,
          _cycleLimit,
          _deriveWeightFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start),
          _fundingCycleToCopy.ballot,
          _start,
          _fundingCycleToCopy.duration,
          _fundingCycleToCopy.target,
          _fundingCycleToCopy.currency,
          _fundingCycleToCopy.fee,
          _fundingCycleToCopy.discountRate,
          0,
          _fundingCycleToCopy.metadata
        );
      ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  A view of the funding cycle that would be created based on the provided one if the project doesn't make a reconfiguration.
 
  @dev
  Returns an empty funding cycle if there can't be a mock funding cycle based on the provided one.
  
  @param _baseFundingCycle The funding cycle that the resulting funding cycle should follow.
  @param _allowMidCycle A flag indicating if the mocked funding cycle is allowed to already be mid cycle.

  @return A mock of what the next funding cycle will be.
*/
function _mockFundingCycleBasedOn(JBFundingCycle memory _baseFundingCycle, bool _allowMidCycle)
  internal
  view
  returns (JBFundingCycle memory)
{
  // Can't mock a non recurring funding cycle.
  if (_baseFundingCycle.discountRate == 201) return _getStructFor(0);

  // If the base has a limit, find the last permanent funding cycle, which is needed to make subsequent calculations.
  // Otherwise, the base is already the latest permanent funding cycle.
  JBFundingCycle memory _latestPermanentFundingCycle = _baseFundingCycle.cycleLimit == 0
    ? _baseFundingCycle
    : _latestPermanentCycleBefore(_baseFundingCycle);
    
  // The distance of the current time to the start of the next possible funding cycle.
  uint256 _timeFromImmediateStartMultiple;

  // If the returned mock cycle must not yet have started, the start time of the mock must be in the future so no need to adjust backwards.
  // If the base funding cycle doesn't have a duration, no adjustment is necessary because the next cycle can start immediately.
  if (!_allowMidCycle || _baseFundingCycle.duration == 0) {
    _timeFromImmediateStartMultiple = 0;
  // If the cycle end time is in the past, the mock should start at a multiple of the last permanent cycle since the cycle ended.
  } else if (_baseFundingCycle.cycleLimit == 0) {
    _timeFromImmediateStartMultiple = _baseFundingCycle.duration * SECONDS_IN_DAY;
  } else {
    // Get the end time of the last cycle.
    uint256 _cycleEnd = _baseFundingCycle.start +
      (_baseFundingCycle.cycleLimit * _baseFundingCycle.duration * SECONDS_IN_DAY);

    // If the cycle end time is in the past, the mock should start at a multiple of the last permanent cycle since the cycle ended.
    _timeFromImmediateStartMultiple = _cycleEnd < block.timestamp
      ? block.timestamp - _cycleEnd
      : _baseFundingCycle.duration * SECONDS_IN_DAY;
  }

  // Derive what the start time should be.
  uint256 _start = _deriveStartFrom(
    _baseFundingCycle,
    _latestPermanentFundingCycle,
    block.timestamp - _timeFromImmediateStartMultiple
  );

  // Derive what the cycle limit should be.
  uint256 _cycleLimit = _deriveCycleLimitFrom(_baseFundingCycle, _start);

  // Derive what the number should be.
  uint256 _number = _deriveNumberFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start);

  // Copy the last permanent funding cycle if the bases' limit is up.
  JBFundingCycle memory _fundingCycleToCopy = _cycleLimit == 0
    ? _latestPermanentFundingCycle
    : _baseFundingCycle;

  return
    JBFundingCycle(
      _idFor(_fundingCycleToCopy.projectId, _number),
      _fundingCycleToCopy.projectId,
      _number,
      _fundingCycleToCopy.id,
      _fundingCycleToCopy.configured,
      _cycleLimit,
      _deriveWeightFrom(_baseFundingCycle, _latestPermanentFundingCycle, _start),
      _fundingCycleToCopy.ballot,
      _start,
      _fundingCycleToCopy.duration,
      _fundingCycleToCopy.target,
      _fundingCycleToCopy.currency,
      _fundingCycleToCopy.fee,
      _fundingCycleToCopy.discountRate,
      0,
      _fundingCycleToCopy.metadata
    );
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



