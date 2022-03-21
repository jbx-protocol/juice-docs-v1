# LaunchProject

Emitted from:

* [`launchProjectFor`](../write/launchprojectfor.md)

## Definition

```solidity
event LaunchProject(uint256 configuration, uint256 projectId, string memo, address caller);
```

* `configuration` is the configuration of the first funding cycle.
* `projectId` is the ID of the project that was launched.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
