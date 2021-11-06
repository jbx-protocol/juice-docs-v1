# usedOverflowAllowanceOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

**The amount of overflow that a project is allowed to tap into on-demand for the specified project configuration.**

_Decreases as projects use of the allowance._

## Definition

```solidity
/**
  @notice 
  The amount of overflow that a project is allowed to tap into on-demand for the specified project configuration. 

  @dev 
  Decreases as projects use of the allowance.

  _projectId The ID of the project to get the current overflow allowance of.
  _configuration The configuration of the during which the allowance applies.
*/
mapping(uint256 => mapping(uint256 => uint256)) public usedOverflowAllowanceOf;
```

* `_projectId` is the ID of the project to get the current overflow allowance of.
* `_configuration` is the configuration of the during which the allowance applies..
* The resulting view function can be accessed externally by anyone.
