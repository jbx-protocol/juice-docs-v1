# MintTokens

Emitted from:

* [`mintTokensOf`](../write/minttokensof.md)

## Definition

```solidity
event MintTokens(
  address indexed beneficiary,
  uint256 indexed projectId,
  uint256 indexed count,
  string memo,
  bool shouldReserveTokens,
  uint256 reservedRate,
  address caller
);
```

* `beneficiary` is the address to which the tokens were minted.
* `projectId` is the ID of the token's project.
* `count` is the number of tokens that were minted.
* `memo` is a note that was attached.
* `shouldReserveTokens` is the indicated intent to reserve tokens as a part of this mint.
* `reservedRate` is the project's current reserved rate.
* `caller` is the address that issued the transaction within which the event was emitted.