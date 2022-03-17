# AddToSetControllerAllowlist

Emitted from:

* [`addToSetControllerAllowList`](../write/addtosetcontrollerallowList.md)

Definition:

```solidity
event AddToSetControllerAllowlist(address indexed allowedAddress, address caller);
```

* `allowedAddress` is the address of the contract that is now allowed to set projects' controllers. 
* `caller` is the address that issued the transaction within which the event was emitted.
