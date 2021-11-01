# overflowAllowanceOf

Contract: [`JBController`](broken-reference)​‌

Interface: [`IJBController`](../../../../../interfaces/ijbcontroller.md)

**The amount of overflow that a project is allowed to tap into on-demand.**

## Definition

```solidity
/**
  @notice 
  The amount of overflow that a project is allowed to tap into on-demand.

  _projectId The ID of the project to get the current overflow allowance of.
  _configuration The configuration of the during which the allowance applies.
  _terminal The terminal managing the overflow.
*/
mapping(uint256 => mapping(uint256 => mapping(IJBTerminal => uint256)))
  public
  override overflowAllowanceOf;
```

* `_projectId` is the ID of the project to get allowances for.
* `_configuration` is the configuration for which the allowances are set.
* `_terminal` is the [`IJBTerminal`](../../../../interfaces/ijbterminal.sol) the allowance is for.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBController` interface.
