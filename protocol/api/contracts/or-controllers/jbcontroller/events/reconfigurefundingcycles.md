# ReconfigureFundingCycles

Emitted from:

* [`reconfigureFundingCyclesOf`](../write/reconfigurefundingcyclesof.md)

## Definition

```solidity
event ReconfigureFundingCycles(
  uint256 configuration,
  uint256 projectId,
  string memo,
  address caller
);
```

* `configuration` is the configuration of the funding cycle that was configured.
* `projectId` is the ID of the project that reconfigured its funding cycles.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
