# usedOverflowAllowanceOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

**The amount of overflow (in the terminal's currency) that a project has used from its allowance during the current funding cycle configuration.**

_Increases as projects use their allowance._

# Definition

```solidity
/**
  @notice 
  The amount of overflow (in the terminal's currency) that a project has used from its allowance during the current funding cycle configuration. 

  @dev 
  Increases as projects use their allowance.

  _projectId The ID of the project to get the used overflow allowance of.
  _configuration The configuration of the during which the allowance applies.
*/
mapping(uint256 => mapping(uint256 => uint256)) public usedOverflowAllowanceOf;
```

* `_projectId` is the ID of the project to get the used overflow allowance of.
* `_configuration` is the configuration of the during which the allowance applies.
* The resulting view function can be accessed externally by anyone.
