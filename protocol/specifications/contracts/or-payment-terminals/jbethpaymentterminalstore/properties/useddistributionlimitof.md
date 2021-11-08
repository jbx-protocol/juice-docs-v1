# usedDistributionLimitOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

**The amount that a project has distributed from its limit during the current funding cycle.**

_Increases as projects use their distribution limit._

## Definition

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

* `_projectId` is the ID of the project to get the used overflow allowance of.
* `_configuration` is the  configuration of the during which the allowance applies.
* The resulting view function can be accessed externally by anyone.