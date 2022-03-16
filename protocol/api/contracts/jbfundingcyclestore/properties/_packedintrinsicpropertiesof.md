# _packedIntrinsicPropertiesOf

Contract: [`JBFundingCycleStore`](../)​‌

**Stores the properties added by the mechanism to manage and schedule each funding cycle, packed into one storage slot.**

# Definition

```solidity
/** 
  @notice
  Stores the properties added by the mechanism to manage and schedule each funding cycle, packed into one storage slot.
  
  _projectId The ID of the project to get instrinsic properties of.
  _configuration The funding cycle configuration to get properties of.
*/
mapping(uint256 => mapping(uint256 => uint256)) private _packedIntrinsicPropertiesOf;
```

* Arguments:
  * `_projectId` is the ID of the project to get instrinsic properties of.
  * `_configuration` is the funding cycle configuration to get properties of.
* The resulting view function is private to this contract.
