# currencyOf 

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

**The currency that overflowAllowances and distribution limits are measured in for a particular funding cycle configuration, applied only to the specified terminal.**

# Definition

```solidity
/**
  @notice 
  The currency that overflowAllowances and distribution limits are measured in for a particular funding cycle configuration, applied only to the specified terminal.

  _projectId The ID of the project to get the currency of.
  _configuration The configuration during which the currency applies.
  _terminal The terminal for which the currency should be used. 
*/
mapping(uint256 => mapping(uint256 => mapping(IJBTerminal => uint256)))
  public
  override currencyOf;
```

* `_projectId` is the ID of the project to get the currency of.
* `_configuration` is the configuration during which the currency applies.
* `_terminal` is the [`IJBTerminal`](../../../../interfaces/ijbterminal.md) for which the currency should be used.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBController` interface.
