# fundingCycleStore

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

**The contract storing all funding cycle configurations.**

# Definition

```solidity
/** 
  @notice 
  The contract storing all funding cycle configurations.
*/
IJBFundingCycleStore public immutable fundingCycleStore;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
