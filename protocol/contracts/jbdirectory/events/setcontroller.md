# SetController

Emitted from:

* [`setControllerOf`](../write/setcontrollerof.md)

Definition:

```solidity
event SetController(uint256 indexed projectId, IJBController indexed controller, address caller);
```

* `projectId` is the ID of the project whose controller was set.
* `controller` is the address of the controller that was set.
* `caller` is the address that issued the transaction within which the event was emitted.
