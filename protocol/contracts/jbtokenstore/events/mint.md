# Mint

Emitted from:

* [`mintFor`](../write/mintfor-1.md)

Definition:

```solidity
event Mint(
  address indexed holder,
  uint256 indexed projectId,
  uint256 amount,
  bool tokensWereClaimed,
  bool preferClaimedTokens,
  address caller
)
```

* `holder`is the address by which the minted tokens are being held.
* `projectId` is the ID of the project to which the minted tokens belong.
* `amount` is the amount of tokens that were minted.
* `tokensWereClaimed` is a flag indicating if the minted tokens were distributed into the holder's wallet.
* `preferClaimedTokens` is a flag indicating if the minting had a preference to claim the tokens into the holder's wallet.
* `caller` is the address that issued the transaction within which the event was emitted.
