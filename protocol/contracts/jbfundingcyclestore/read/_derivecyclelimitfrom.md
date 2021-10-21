# _deriveCycleLimitFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The number of the next funding cycle given the specified funding cycle.**

# Definition

```solidity
function _deriveCycleLimitFrom(JBFundingCycle memory _fundingCycle, uint256 _start)
  private
  pure
  returns (uint256) { ... }
```

* Arguments:
  * `_fundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
  * `_start` is a time that the cycle having a cycle limit derived for starts.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the cycle limit to use.

# Body 

1. There's no longer a cycle limit if the provided cycle has a limit of 1, or if it has no duration.

   ```solidity
   // There's no longer a cycle limit if the provided cycle limit is 1, or if it has no duration.
   if (_fundingCycle.cycleLimit <= 1 || _fundingCycle.duration == 0) return 0;
   ```


2. Get a reference to the number of cycles that can fit between the funding cycle's start, and the provided start.  

   ```solidity
   // Get a reference to the number of cycles that can fit between the funding cycle's start, and the provided start.
   uint256 _cycles = (_start - _fundingCycle.start) / (_fundingCycle.duration * _SECONDS_IN_DAY);
   ```

   _Internal references:_

   * [`_SECONDS_IN_DAY`](../properties/_seconds_in_day.md)


3. There's no longer a cycle limit if more cycles have passed than the provided cycle's limit.

   ```solidity
   // If all of the cycle limit has passed, return 0.
   if (_cycles >= _fundingCycle.cycleLimit) return 0;
   ```


4. Return the subtracted number of cycles that have passed from the provided cycle's limit.

   ```solidity
   // Subtract the number of cycles that have passed from the limit.
   return _fundingCycle.cycleLimit - _cycles;
   ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The limited number of times the next funding cycle configuration can be active given the specified funding cycle.

  @param _fundingCycle The funding cycle to make the calculation with.
  @param _start The start time to derive cycles remaining for.

  @return start The inclusive nunmber of cycles remaining.
*/
function _deriveCycleLimitFrom(JBFundingCycle memory _fundingCycle, uint256 _start)
  private
  pure
  returns (uint256)
{
  // There's no longer a cycle limit if the provided cycle limit is 1, or if it has no duration.
  if (_fundingCycle.cycleLimit <= 1 || _fundingCycle.duration == 0) return 0;
  
  // Get a reference to the number of cycles that can fit between the funding cycle's start, and the provided start.
  uint256 _cycles = (_start - _fundingCycle.start) / (_fundingCycle.duration * _SECONDS_IN_DAY);
  
  // If all of the cycle limit has passed, return 0.
  if (_cycles >= _fundingCycle.cycleLimit) return 0;
  
  // Subtract the number of cycles that have passed from the limit.
  return _fundingCycle.cycleLimit - _cycles;
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

