# MAX\_CYCLE\_LIMIT

Contract:[`JBFundingCycleStore`](../)​‌

**The maximum cycle limit value that can be set.**

_The cycle limit is a property within funding cycle configurations._

Definition:

```solidity
/** 
  @notice 
  The maximum cycle limit value that can be set.
*/
uint256 public constant override MAX_CYCLE_LIMIT = 32;
```

* This value cannot change.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBFundingCycleStore` interface.

