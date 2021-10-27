# _targetOf

Contract: [`JBFundingCycleStore`](../)​‌

**Stores the amount that each funding cycle can tap during the funding cycle.**

## Definition

```solidity
/** 
  @notice
  Stores the amount that each funding cycle can tap during the funding cycle.
  
  _projectId The ID of the project to get the target of.
*/
mapping(uint256 => uint256) private _targetOf;
```

* `_projectId` is the ID of the project to get the target of.
* The resulting view function is private to this contract.
