# distributionLimitOf 

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../../../protocol/interfaces/ijbcontroller.md)

**The amount of that a project can withdraw per funding cycle.**

## Definition

```solidity
/**
  @notice 
  The amount of that a project can withdraw per funding cycle.

  _projectId The ID of the project to get the current distribution allowance of.
  _configuration The configuration during which the distribution limit applies.
  _terminal The terminal managing distribution.
*/
mapping(uint256 => mapping(uint256 => mapping(IJBTerminal => uint256)))
  public
  override distributionLimitOf;
```

* `_projectId` is the ID of the project to get the current distribution limit of.
* `_configuration` is the configuration during which the distribution limit applies.
* `_terminal` is the [`IJBTerminal`](../../../../../../protocol/specifications/interfaces/ijbterminal.sol) from which distributions are being limited.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBController` interface.
