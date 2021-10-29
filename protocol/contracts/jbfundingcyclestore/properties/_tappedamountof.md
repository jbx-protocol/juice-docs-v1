# \_tappedAmountOf

Contract: [`JBFundingCycleStore`](../)​

**Stores the amount that has been tapped within each funding cycle.**

## Definition

```solidity
/** 
  @notice
  Stores the amount that has been tapped within each funding cycle.
  
  _projectId The ID of the project to get the tapped amount of.
*/
mapping(uint256 => uint256) private _tappedAmountOf;
```

* `_projectId` is the ID of the project to get the tapped amount of.
* The resulting view function is private to this contract.
