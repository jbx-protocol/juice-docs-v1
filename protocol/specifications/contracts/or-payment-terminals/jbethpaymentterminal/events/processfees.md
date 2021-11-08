# ProcessFees

Emitted from:

* [`processFees`](../write/processfees.md)

# Definition

```solidity
event ProcessFees(uint256 indexed projectId, JBFee[] fees, address caller);
```

* `projectId` is the ID of the project whos fees were processed.
* `fees` is an array of [`JBFee`](../../../../data-structures/jbfee.md) data structures that were processed.
* `caller` is the address that issued the transaction within which the event was emitted.
