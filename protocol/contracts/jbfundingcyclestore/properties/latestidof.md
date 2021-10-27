# latestIdOf

Contract: [`JBFundingCycleStore`](../)​‌

**The ID of the latest stored funding cycle for each project.**

## Definition

```solidity
/** 
  @notice 
  The ID of the latest stored funding cycle for each project.
  
  _projectId The ID of the project to get the latest funding cycle ID of.
*/
mapping(uint256 => uint256) public override latestIdOf;
```

* `_projectId` is the ID of the project to get the latest funding cycle ID of.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBFundingCycleStore` interface.
