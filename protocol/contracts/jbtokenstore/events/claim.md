# Claim

Emitted from:

* [`claimFor`](../write/burnfrom.md)

Definition:

```solidity
event Claim(address indexed holder, uint256 indexed projectId, uint256 amount, address caller)
```

* `holder`is the address to which the tokens being claimed belong.
* `projectId` is the ID of the project to which the claimed tokens belong.
* `amount` is the amount of tokens that were claimed.
* `caller` is the address that issued the transaction within which the event was emitted.
