# SetOverflowAllowance

Emitted from:

* [`launchProjectFor`](../write/launchprojectfor.md)
* [`reconfigureFundingCyclesOf`](../write/reconfigurefundingcyclesof.md)

## Definition

```solidity
event SetOverflowAllowance(
  uint256 indexed projectId,
  uint256 indexed configuration,
  JBOverflowAllowance allowance,
  address caller
);
```

* `projectId` is the ID of the project who has set an overflow allowance.
* `configuration` is the configuration during which the overflow allowance is valid.
* `allowance` is the [`JBOverflowAllowance`](../../../../../data-structures/jboverflowallowance.md) data structure.
* `caller` is the address that issued the transaction within which the event was emitted.
