# IJBSplitAllocator

```solidity
interface IJBSplitAllocator {
  function allocate(
    uint256 _amount,
    uint256 _projectId,
    uint256 _group,
    JBSplit calldata _split
  ) external payable;
}
```
