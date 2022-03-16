# _packedUserPropertiesOf

Contract: [`JBFundingCycleStore`](../)​‌

**Stores the user defined properties of each funding cycle, packed into one storage slot.**

# Definition

```solidity
/** 
  @notice
  Stores the user defined properties of each funding cycle, packed into one storage slot.

  _projectId The ID of the project to get properties of.
  _configuration The funding cycle configuration to get properties of.
*/
mapping(uint256 => mapping(uint256 => uint256)) private _packedUserPropertiesOf;
```

* Arguments:
  * `_projectId` is the ID of the project to get properties of.
  * `_configuration` is the funding cycle configuration to get properties of.
* The resulting view function is private to this contract.
