# \_packedIntrinsicPropertiesOf

Contract:[`JBFundingCycleStore`](../)​‌

**Stores the properties added by the mechanism to manage and schedule each funding cycle, packed into one storage slot.**

# Definition

```solidity
/** 
  @notice
  Stores the properties added by the mechanism to manage and schedule each funding cycle, packed into one storage slot.
  
  [_projectId]
*/
mapping(uint256 => uint256) private _packedIntrinsicPropertiesOf;
```

* `uint256` is the the ID of the project to get the`_packedIntrinsicPropertiesOf`.
* The resulting view function is private to this contract.
