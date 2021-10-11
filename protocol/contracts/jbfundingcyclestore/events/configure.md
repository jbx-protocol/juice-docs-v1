# Configure

Emitted from:

* [`configureFor`](../write/configurefor.md)

Definition:

```solidity
event Configure(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  uint256 indexed reconfigured,
  FundingCycleProperties properties,
  uint256 metadata,
  address caller
);
```

* `fundingCycleId` is the ID of the funding cycle that was configured.
* `projectId` is the ID of the project to which the configured funding cycle belongs.
* `reconfigured` is the time at which the configuration occurred.
* `properties` are the properties of the configuration.
* `metadata` is the metadata attached the configuration.
* `caller` is the address that issued the transaction within which the event was emitted.
