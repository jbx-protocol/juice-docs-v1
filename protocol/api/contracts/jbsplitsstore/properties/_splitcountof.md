# _splitCountOf

Contract: [`JBSplitsStore`](../)​‌

**The number of splits currently set for each project ID's configurations.**

# Definition

```solidity
/** 
  @notice
  The number of splits currently set for each project ID's configurations.

  _projectId The ID of the project to get the split count for.
  _domain An identifier within which the returned splits should be considered active.
  _group The identifying group of the splits.
*/
mapping(uint256 => mapping(uint256 => mapping(uint256 => uint256))) private _splitCountOf;
```

* `_projectId` is the ID of the project to get the split count for.
* `_domain` is an identifier within which the returned splits should be considered active.
* `_group` is the identifying group of the splits.
* Returns the number of splits the project has under the specified domain and gorup.
* The resulting view function is private to this contract.
