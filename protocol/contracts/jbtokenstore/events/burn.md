# Burn

Emitted from:

* [`burnFrom`](../write/burn.md)

Definition:

```solidity
event Burn(
  address indexed holder,
  uint256 indexed projectId,
  uint256 amount,
  uint256 unclaimedTokenBalance,
  bool preferClaimedTokens,
  address caller
)
```

* `holder`is the address by which the burned tokens were being held.
* `projectId` is the ID of the project to which the burned tokens belong.
* `amount` is the amount of tokens that were burned.
* `unclaimedTokenBalance` is the amount of unclaimed tokens the holder had a balance of at the time of burning.
* `preferClaimedTokens` is a flag indicating if the burning had a preference to use claimed tokens from the holder's wallet.
* `caller` is the address that issued the transaction within which the event was emitted.
