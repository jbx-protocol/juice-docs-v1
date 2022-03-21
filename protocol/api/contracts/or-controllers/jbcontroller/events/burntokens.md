# BurnTokens

Emitted from:

* [`burnTokensOf`](../write/burntokensof.md)

## Definition

```solidity
event BurnTokens(
  address indexed holder,
  uint256 indexed projectId,
  uint256 tokenCount,
  string memo,
  address caller
);
```

* `holder` is the address from which the tokens were burned.
* `projectId` is the ID of the token's project.
* `tokenCount` is the number of tokens that were burned.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
