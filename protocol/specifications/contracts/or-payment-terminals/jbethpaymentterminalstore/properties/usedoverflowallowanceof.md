# usedOverflowAllowanceOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

**The amount of overflow that a project is allowed to tap into on-demand for the specified project configuration.**

_Increases as projects use their distribution limit._

# Definition

```solidity
/**
  @notice 
  The amount that a project has distributed from its limit during the current funding cycle. 

  @dev 
  Increases as projects use their distribution limit.

  _projectId The ID of the project to get the used distribution limit of.
  _configuration The configuration of the during which the disitrution limit applies.
*/
mapping(uint256 => mapping(uint256 => uint256)) public usedDistributionLimitOf;
```

* `_projectId` is the ID of the project to get the used distribution limit of.
* `_configuration` is the configuration of the during which the disitrution limit applies.
* The resulting view function can be accessed externally by anyone.
