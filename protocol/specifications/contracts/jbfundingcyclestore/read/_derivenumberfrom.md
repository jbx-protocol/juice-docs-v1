# \_deriveNumberFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The number of the next funding cycle given the specified funding cycle.**

# Definition

```solidity
function _deriveNumberFrom(JBFundingCycle memory _baseFundingCycle, uint256 _start) 
  private 
  pure 
  returns (uint256) { ... }
```

* Arguments:
  * `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
  * `_start` is a time that the cycle having a weight derived for starts.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the funding cycle number.

# Body

1.  If the `_baseFundingCycle` doesn't have a duration, the next number is 1 more than the base's number.

    ```solidity
    // A subsequent cycle to one with a duration of 0 should be the next number.
    if (_baseFundingCycle.duration == 0) return _baseFundingCycle.number + 1;
    ```
2.  Get a reference to how long after the `_baseFundingCycle`'s start the specified `_start` time is. The goal will be to see how many cycles have passed within this time distance.

    ```solidity
    // The difference between the start of the base funding cycle and the proposed start.
    uint256 _startDistance = _start - _baseFundingCycle.start;
    ```
3.  Return the number of base cycles that fit in the base distance.

    ```solidity
    // Find the number of base cycles that fit in the start distance.
    return _baseFundingCycle.number + (_startDistance / _baseFundingCycle.duration);
    ```

    _Internal references:_

    * [`_SECONDS_IN_DAY`](../properties/\_seconds\_in\_day.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The number of the next funding cycle given the specified funding cycle.

  @param _baseFundingCycle The funding cycle to make the calculation with.
  @param _start The start time to derive a number for.

  @return The next number.
*/
function _deriveNumberFrom(JBFundingCycle memory _baseFundingCycle, uint256 _start) 
  private 
  pure 
  returns (uint256) 
{
  // A subsequent cycle to one with a duration of 0 should be the next number.
  if (_baseFundingCycle.duration == 0) return _baseFundingCycle.number + 1;

  // The difference between the start of the base funding cycle and the proposed start.
  uint256 _startDistance = _start - _baseFundingCycle.start;

  // Find the number of base cycles that fit in the start distance.
  return _baseFundingCycle.number + (_startDistance / _baseFundingCycle.duration);
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
