# overflowAllowanceOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

**The amount of overflow that a project is allowed to tap into on-demand.**

## Definition

```solidity
/**
  @notice 
  The amount of overflow that a project is allowed to tap into on-demand.

  @dev
  [_projectId][_configuration][_terminal]

  _projectId The ID of the project to get the current overflow allowance of.
  _configuration The configuration of the during which the allowance applies.
  _terminal The terminal managing the overflow.

  @return The current overflow allowance for the specified project configuration. Decreases as projects use of the allowance.
*/
mapping(uint256 => mapping(uint256 => mapping(IJBTerminal => uint256)))
  public
  override overflowAllowanceOf;
```

* `uint256` is the ID of the project to get allowances for.
* `uint256` is the configuration for which the allowances are set.
* `IJBTerminal` is the terminal the allowance is for.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBController` interface.