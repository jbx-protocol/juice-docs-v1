# latestIdOf

Contract:[`JBFundingCycleStore`](../)​‌

**The ID of the latest stored funding cycle for each project.**

Definition:

```solidity
/** 
  @notice 
  The ID of the latest stored funding cycle for each project.
  
  [_projectId]
*/
mapping(uint256 => uint256) public override latestIdOf;
```

* `uint256` is the the ID of the project to get the `latestOf`.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBFundingCycleStore` interface.
