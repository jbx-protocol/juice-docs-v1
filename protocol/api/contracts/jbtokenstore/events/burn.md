# Burn

Emitted from:

* [`burnFrom`](../write/burnfrom.md)

## Definition

```solidity
event Burn(
  address indexed holder,
  uint256 indexed projectId,
  uint256 amount,
  uint256 initialUnclaimedBalance
  uint256 initialClaimedBalance,
  bool preferClaimedTokens,
  address caller
)
```

* `holder`is the address from which the tokens were burned.
* `projectId` is the ID of the project to which the burned tokens belong.
* `amount` is the amount of tokens that were burned.
* `initialUnclaimedBalance` is the amount of unclaimed tokens the holder had a balance of at the time of burning.
* `initialClaimedBalance` is the amount of claimed tokens the holder had a balance of at the time of burning.
* `preferClaimedTokens` is a flag indicating if the burning had a preference to burn claimed tokens from the holder's wallet.
* `caller` is the address that issued the transaction within which the event was emitted.
