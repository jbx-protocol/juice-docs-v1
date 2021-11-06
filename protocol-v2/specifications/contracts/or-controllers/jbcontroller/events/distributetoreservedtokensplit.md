# DistributeToReservedTokenSplit

Emitted from:

* [`distributeReservedTokensOf`](../write/distributereservedtokensof.md)

## Definition

```solidity
event DistributeToReservedTokenSplit(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  JBSplit split,
  uint256 count,
  address caller
);
```

* `fundingCycleId` is the ID of the funding cycle during which the reserved tokens were distributed.
* `projectId` is the ID of the token's project.
* `split` is the [`JBSplit`](../../../../../../protocol/data-structures/jbsplit.md) data structure that the split was made for.
* `projectOwner` is the address that received any leftover tokens after splits were applied.
* `count` is the total number of tokens that were distributed.
* `caller` is the address that issued the transaction within which the event was emitted.
