# Issue

Emitted from:

* [`issueFor`](../write/issuefor.md)

## Definition

```solidity
event Issue(
  uint256 indexed projectId,
  IJBToken indexed token,
  string name,
  string symbol,
  address caller
)
```

* `projectId` is the ID of the project to which the issued tokens belong.
* `token` is the ID of the address of the newly issued token.
* `name` is the name of the token.
* `symbol` is the symbol of the token.
* `caller` is the address that issued the transaction within which the event was emitted.
