# \_terminalsOf

Contract:[`JBDirectory`](../)‌

**For each project ID, the terminals that are currently managing its funds.**

# Definition

```solidity
/** 
  @notice 
  For each project ID, the terminals that are currently managing its funds.

  [_projectId]
*/
mapping(uint256 => IJBTerminal[]) private _terminalsOf;
```

* `_projectId` is the ID of the project to get terminals of.
* The resulting view function is private to this contract.
