# _processedTokenTrackerOf

Contract: [`JBController`](../)​‌

**The difference between the processed token tracker of a project and the project's token's total supply is the amount of tokens that still need to have reserves minted against them.**

# Definition

```solidity
/** 
  @notice
  The difference between the processed token tracker of a project and the project's token's total supply is the amount of tokens that still need to have reserves minted against them.

  _projectId The ID of the project to get the tracker of.
*/
mapping(uint256 => int256) private _processedTokenTrackerOf;
```

* `_projectId` is the ID of the project to get the tracker of.
* The resulting view function is private to this contract.
