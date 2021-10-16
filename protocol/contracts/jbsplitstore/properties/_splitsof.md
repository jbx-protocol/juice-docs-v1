# \_splitsOf

Contract:[`JBSplitStore`](../)​‌

**All splits for each project ID's configurations.**

# Definition

```solidity
/** 
  @notice
  All splits for each project ID's configurations.
*/
mapping(uint256 => mapping(uint256 => mapping(uint256 => JBSplit[]))) private _splitsOf;
```

* `uint256` is the ID of the project to get splits for.
* `uint256` is an identifier within which the returned splits should be considered active.
* `uint256` is the identifying group of the splits.
* Returns an array of [`JBSplit`](../../../data-structures/jbsplit.md)s.
* The resulting view function is private to this contract.
