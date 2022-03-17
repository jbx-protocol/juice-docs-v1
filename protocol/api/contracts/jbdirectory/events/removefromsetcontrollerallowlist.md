# RemoveFromSetControllerAllowlist

Emitted from:

* [`removeFromSetControllerAllowList`](../write/removedfromsetcontrollerallowList.md)

Definition:

```solidity
event RemoveFromSetControllerAllowlist(address indexed removedAddress, address caller);
```

* `removedAddress` is the address of the contract that is no longer allowed to set projects' controllers.
* `caller` is the address that issued the transaction within which the event was emitted.
