# \_deriveWeightFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The accumulated weight change since the specified funding cycle.**

# Definition

```solidity
function _deriveWeightFrom(JBFundingCycle memory _baseFundingCycle, uint256 _start) 
  private 
  pure 
  returns (uint256 weight) { ... }
```

* Arguments:
  * `_baseFundingCycle` is The [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to make the calculation for.
  * `_start` is a time that the cycle having a weight derived for starts.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns a weight with 18 decimal places.

# Body

1.  If the base funding cycle has no duration, the derived weight should be calculated from it no matter how much time has passed since it was active. The `discountRate` property in a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md)is out of 10000. Discount rates represent a number between 0-100%, with 0.01% fidelity, so the calculation must be made out of 10000.

    ```solidity
    // A subsequent cycle to one with a duration of 0 should have the next possible weight.
    if (_baseFundingCycle.duration == 0)
      return PRBMath.mulDiv(_baseFundingCycle.weight, 10000 - _baseFundingCycle.discountRate, 10000);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)\
        `.mulDiv(...)`

2.  The calculations that follow will progressively apply discount rates to the `_baseFundingCycle`'s weight to arrive at the correct weight to return.

    ```solidity
    // The weight should be based off the base funding cycle's weight.
    weight = _baseFundingCycle.weight;
    ```
3.  If the base doesn't have a discount rate, the original weight won't change and should be returned.

    ```solidity
    // If the discount is 0, the weight doesn't change.
    if (_baseFundingCycle.discountRate == 0) return weight;
    ```
4.  Get a reference to how long after the `_baseFundingCycle`'s start the specified `_start` time is. The goal will be to see how many cycles have passed within this time distance.

    ```solidity
    // The difference between the start of the base funding cycle and the proposed start.
    uint256 _startDistance = _start - _baseFundingCycle.start;
    ```
5.  Apply the `_baseFundingCycle`'s discount rate. Apply the rate as many times as there have been cycles within the `_startDistance`.

    ```solidity
    // Apply the base funding cycle's discount rate, if necessary.
    uint256 _discountMultiple = _startDistance / (_baseFundingCycle.duration * _SECONDS_IN_DAY);

    for (uint256 i = 0; i < _discountMultiple; i++)
      // The number of times to apply the discount rate.
      // Base the new weight on the specified funding cycle's weight.
      weight = PRBMath.mulDiv(weight, 10000 - _baseFundingCycle.discountRate, 10000);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)\
        `.mulDiv(...)`

    _Internal references:_

    * [`_SECONDS_IN_DAY`](../properties/\_seconds\_in\_day.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The accumulated weight change since the specified funding cycle.

  @param _baseFundingCycle The funding cycle to make the calculation with.
  @param _start The start time to derive a weight for.

  @return weight The next weight.
*/
function _deriveWeightFrom(
  JBFundingCycle memory _baseFundingCycle,
  uint256 _start
) private pure returns (uint256 weight) {
  // A subsequent cycle to one with a duration of 0 should have the next possible weight.
  if (_baseFundingCycle.duration == 0)
    return PRBMath.mulDiv(_baseFundingCycle.weight, 10000 - _baseFundingCycle.discountRate, 10000);

  // The weight should be based off the base funding cycle's weight.
  weight = _baseFundingCycle.weight;
  
  // If the discount is 0, the weight doesn't change.
  if (_baseFundingCycle.discountRate == 0) return weight;

  // The difference between the start of the base funding cycle and the proposed start.
  uint256 _startDistance = _start - _baseFundingCycle.start;
  
  // Apply the base funding cycle's discount rate for each cycle that has passed.
  uint256 _discountMultiple = _startDistance / (_baseFundingCycle.duration * _SECONDS_IN_DAY);

  for (uint256 i = 0; i < _discountMultiple; i++)
    // The number of times to apply the discount rate.
    // Base the new weight on the specified funding cycle's weight.
    weight = PRBMath.mulDiv(weight, 10000 - _baseFundingCycle.discountRate, 10000);
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
