# DistributeReservedTokens

Emitted from:

* [`distributeReservedTokensOf`](../write/distributereservedtokensof.md)

## Definition

```solidity
event DistributeReservedTokens(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  address indexed projectOwner,
  uint256 count,
  uint256 projectOwnerTokenCount,
  string memo,
  address caller
);
```

* `fundingCycleId` is the ID of the funding cycle during which the reserved tokens were distributed.
* `projectId` is the ID of the token's project.
* `projectOwner` is the address that received any leftover tokens after splits were applied.
* `count` is the total number of tokens that were distributed.
* `projectOwnerTokenCount` is the number of tokens that were distributed to the project owner.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
