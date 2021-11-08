# _splitsOf

Contract: [`JBSplitsStore`](../)​‌

**All splits for each project ID's configurations.**

# Definition

```solidity
/** 
  @notice
  All splits for each project ID's configurations.

  _projectId is The ID of the project to get splits for.
  _domain is An identifier within which the returned splits should be considered active.
  _group The identifying group of the splits.
*/
mapping(uint256 => mapping(uint256 => mapping(uint256 => JBSplit[]))) private _splitsOf;
```

* `_projectId` is the ID of the project to get splits for.
* `_domain` is an identifier within which the returned splits should be considered active.
* `_group` is the identifying group of the splits.
* Returns an array of [`JBSplit`](../../../data-structures/jbsplit.md)s.
* The resulting view function is private to this contract.
