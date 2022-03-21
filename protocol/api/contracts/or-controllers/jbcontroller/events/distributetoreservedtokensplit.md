# DistributeToReservedTokenSplit

Emitted from:

* [`_distributeToReservedTokenSplitsOf`](../write/_distributetoreservedtokensplitsof.md)

## Definition

```solidity
event DistributeToReservedTokenSplit(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCycleNumber,
  uint256 indexed projectId,
  JBSplit split,
  uint256 tokenCount,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which the reserved tokens were distributed to the split.
* `fundingCycleNumber` is the number of the funding cycle during which the reserved tokens were distributed to the split.
* `projectId` is the ID of the token's project.
* `split` is the [`JBSplit`](../../../../data-structures/jbsplit.md) that received reserved tokens.
* `tokenCount` is the number of tokens that were distributed to the split.
* `caller` is the address that issued the transaction within which the event was emitted.
