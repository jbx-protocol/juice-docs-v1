# SetOverflowAllowance

Emitted from:

* [`launchProjectFor`](../write/launchprojectfor.md)
* [`reconfigureFundingCyclesOf`](../write/reconfigurefundingcyclesof.md)

## Definition

```solidity
event SetFundAccessConstraints(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCycleNumber,
  uint256 indexed projectId,
  JBFundAccessConstraints constraints,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which the overflow allowance is valid.
* `fundingCycleNumber` is the number of the funding cycle during which the overflow allowance is valid.
* `projectId` is the ID of the project who has set an overflow allowance.
* `constraints` is the [`JBFundAccessConstraints`](../../../../data-structures/jbfundaccessconstraints.md) data structure.
* `caller` is the address that issued the transaction within which the event was emitted.
