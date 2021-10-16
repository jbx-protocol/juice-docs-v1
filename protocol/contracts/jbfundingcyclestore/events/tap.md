# Tap

Emitted from:

* [`tapFrom`](../write/tapfrom.md)

# Definition

```solidity
event Tap(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  uint256 amount,
  uint256 newTappedAmount,
  address caller
);
```

* `fundingCycleId` is the ID of the funding cycle that was tapped.
* `projectId` is the ID of the project to which the tapped funding cycle belongs.
* `amount` is the amount that was tapped.
* `newTappedAmount` is the total amount that has now been tapped from the funding cycle.
* `caller` is the address that issued the transaction within which the event was emitted.
