# Configure

Emitted from:

* [`configureFor`](../write/configurefor.md)

## Definition

```solidity
event Configure(
  uint256 indexed configuration,
  uint256 indexed projectId,
  JBFundingCycleData data,
  uint256 metadata,
  uint256 mustStartAtOrAfter,
  address caller
);
```

* `configuration` is the funding cycle configuration that was successfully updated.
* `projectId` is the ID of the project to which the configured funding cycle belongs.
* `data` are the data of the configuration.
* `metadata` is the metadata attached the configuration.
* `mustStartAtOrAfter` is the time before which the initialized funding cycle can't start.
* `caller` is the address that issued the transaction within which the event was emitted.
