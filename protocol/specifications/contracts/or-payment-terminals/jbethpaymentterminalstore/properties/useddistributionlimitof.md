# usedDistributionLimitOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

**The amount (in the terminal's currency) that a project has distributed from its limit during the current funding cycle.**

_Increases as projects use their distribution limit._

# Definition

```solidity
/**
  @notice 
  The amount (in the terminal's currency) that a project has distributed from its limit during the current funding cycle.

  @dev 
  Increases as projects use their distribution limit.

  _projectId The ID of the project to get the used distribution limit of.
  _number The number representing the funding cycle.
*/
mapping(uint256 => mapping(uint256 => uint256)) public usedDistributionLimitOf;
```

* `_projectId` is the ID of the project to get the used overflow allowance of.
* `_number` is the number representing the funding cycle.
* The resulting view function can be accessed externally by anyone.