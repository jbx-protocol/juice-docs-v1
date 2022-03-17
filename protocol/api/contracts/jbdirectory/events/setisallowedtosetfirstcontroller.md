# SetIsAllowedToSetFirstController

Emitted from:

* [`setIsAllowedToSetFirstController`](../write/setisallowedtosetfirstcontroller.md)

Definition:

```solidity
event SetIsAllowedToSetFirstController(address indexed addr, bool indexed flag, address caller);
```

* `addr` is the address of the contract that is being given or revoked allowance to set projects' first controllers on their behalf. 
* `flag` is the value that was set, either `true` or `false`.
* `caller` is the address that issued the transaction within which the event was emitted.
