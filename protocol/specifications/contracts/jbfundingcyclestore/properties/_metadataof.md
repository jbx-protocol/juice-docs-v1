# _metadataOf

Contract: [`JBFundingCycleStore`](../)​‌

**Stores the metadata for each funding cycle, packed into one storage slot.**

## Definition

```solidity
/** 
  @notice
  Stores the metadata for each funding cycle, packed into one storage slot.
  
  _projectId The ID of the project to get the`_metadataOf`.
*/
mapping(uint256 => uint256) private _metadataOf;
```

* `_projectId` is the ID of the project to get the`_metadataOf`.
* The resulting view function is private to this contract.
