# \_packedsplitParts1Of

Contract: [`JBSplitsStore`](../)​‌

**Packed data of splits for each project ID's configurations.**

# Definition

```solidity
/** 
  @notice
  Packed data of splits for each project ID's configurations.

  _projectId The ID of the project to get packed splits data for.
  _domain An identifier within which the returned splits should be considered active.
  _group The identifying group of the splits.
  _index The indexed order that the split was set at.
*/
mapping(uint256 => mapping(uint256 => mapping(uint256 => mapping(uint256 => uint256))))
  private _packedSplitParts1Of;
```

* `_projectId` is the ID of the project to get packed splits data for.
* `_domain` is an identifier within which the returned splits should be considered active.
* `_group` is the identifying group of the splits.
* `_index` is the indexed order that the split was set at.
* Returns packed split data.
* The resulting view function is private to this contract.
