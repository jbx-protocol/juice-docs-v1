# JBTokenAmount

```solidity
struct JBSplitAllocationData {
  // The amount being sent to the split allocator, as a fixed point number.
  uint256 amount;
  // The number of decimals in the amount.
  uint256 decimals;
  // The project to which the split belongs.
  uint256 projectId;
  // The group to which the split belongs.
  uint256 group;
  // The split that caused the allocation.
  JBSplit split;
}
```