# _packedOverflowAllowanceDataOf

Contract: [`JBController`](../)​‌

**Data regarding the overflow allowance of a project during a configuration.**

_bits 0-247: The amount of overflow that a project is allowed to tap into on-demand throughout the configuration._

_bits 248-255: The currency of the amount of overflow that a project is allowed to tap._

# Definition

```solidity
/**
  @notice
  Data regarding the overflow allowance of a project during a configuration.

  @dev
  bits 0-247: The amount of overflow that a project is allowed to tap into on-demand throughout the configuration.

  @dev
  bits 248-255: The currency of the amount of overflow that a project is allowed to tap.

  _projectId The ID of the project to get the packed overflow allowance data of.
  _configuration The configuration during which the packed overflow allowance data applies.
  _terminal The terminal managing the overflow.
*/
mapping(uint256 => mapping(uint256 => mapping(IJBPaymentTerminal => uint256)))
  private _packedOverflowAllowanceDataOf;
```

* `_projectId` is the ID of the project to get the packed overflow allowance data of.
* `_configuration` is the configuration during which the packed overflow allowance data applies.
* `_terminal` is the [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md) managing the overflow.
* The resulting view function is private to this contract.