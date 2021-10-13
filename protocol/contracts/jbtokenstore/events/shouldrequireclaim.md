# ShouldRequireClaimFor

Emitted from:

* [`shouldRequireClaimingFor`](../write/shouldrequireclaimingfor.md)

Definition:

```solidity
event ShouldRequireClaim(uint256 indexed projectId, bool indexed flag, address caller)
```

* `projectId` is the ID of the project which is requiring claimed tokens or not.
* `flag` is whether or not claimed tokens are being required.
* `caller` is the address that issued the transaction within which the event was emitted.
