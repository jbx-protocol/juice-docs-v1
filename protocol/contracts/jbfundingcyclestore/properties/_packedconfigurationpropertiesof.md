# \_packedConfigurationPropertiesOf

Contract: [`JBFundingCycleStore`](../)​‌

**Stores the reconfiguration properties of each funding cycle, packed into one storage slot.**

## Definition

```solidity
/** 
  @notice
  Stores the reconfiguration properties of each funding cycle, packed into one storage slot.
  
  _projectId The ID of the project to get configuration properties of.
*/
mapping(uint256 => uint256) private _packedConfigurationPropertiesOf;
```

* `_projectId` is the ID of the project to get configuration properties of.
* The resulting view function is private to this contract.
