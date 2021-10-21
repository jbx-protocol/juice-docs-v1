# _packedConfigurationPropertiesOf

Contract:[`JBFundingCycleStore`](../)​‌

**Stores the reconfiguration properties of each funding cycle, packed into one storage slot.**

# Definition

```solidity
/** 
  @notice
  Stores the reconfiguration properties of each funding cycle, packed into one storage slot.
  
  [_projectId]
*/
mapping(uint256 => uint256) private _packedConfigurationPropertiesOf;
```

* `uint256` is the the ID of the project to get the`_packedConfigurationPropertiesOf`.
* The resulting view function is private to this contract.
