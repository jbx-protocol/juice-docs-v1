# usedOverflowAllowanceOf

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)

**The amount of funds that a project has used from its allowance during the current funding cycle configuration for each terminal, in terms of the overflow allowance's currency.**

_Increases as projects use their allowance._

_The used allowance limit is represented as a fixed point number with the same amount of decimals as its relative terminal._

# Definition

```solidity
/**
  @notice
  The amount of funds that a project has used from its allowance during the current funding cycle configuration for each terminal, in terms of the overflow allowance's currency.

  @dev
  Increases as projects use their allowance.

  @dev
  The used allowance limit is represented as a fixed point number with the same amount of decimals as its relative terminal.

  _terminal The terminal to which the overflow allowance applies.
  _projectId The ID of the project to get the used overflow allowance of.
  _configuration The configuration of the during which the allowance was used.
*/
mapping(IJBPaymentTerminal => mapping(uint256 => mapping(uint256 => uint256)))
  public
  override usedOverflowAllowanceOf;
```

* Arguments:
  * `_terminal` is the terminal to which the overflow allowance applies.
  * `_projectId` is the ID of the project to get the used overflow allowance of.
  * `_configuration` is the configuration of the during which the allowance was used.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md) interface.
