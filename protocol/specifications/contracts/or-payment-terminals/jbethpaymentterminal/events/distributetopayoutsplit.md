# DistributeToPayoutSplit

Emitted from:

* [`_distributeToPayoutSplitsOf`](../write/_distributetopayoutsplitsof.md)

## Definition

```solidity
event DistributeToPayoutSplit(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCycleNumber,
  uint256 indexed projectId,
  JBSplit split,
  uint256 amount,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which a payout split was distributed.
* `fundingCycleNumber` is the number of the funding cycle during which a payout split was distributed.
* `projectId` is the ID of the project that had a payout split distributed.
* `split` is the [`JBSplit`](../../../../data-structures/jbsplit.md) to which the distribution was made.
* `amount` is the total ETH amount that was distributed to the split.
* `caller` is the address that issued the transaction within which the event was emitted.
