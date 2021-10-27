# AddToBalance

Emitted from:

* [`addToBalanceOf`](../write/addtobalanceof.md)

## Definition

```solidity
event AddToBalance(uint256 indexed projectId, uint256 amount, string memo, address caller);
```

* `projectId` is the ID of the balance's project.
* `amount` is the amount being added.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.