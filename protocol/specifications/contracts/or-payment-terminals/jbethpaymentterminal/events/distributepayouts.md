# DistributePayouts

Emitted from:

* [`distributePayoutsOf`](../write/distributepayoutsof.md)

## Definition

```solidity
event DistributePayouts(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  address projectOwner,
  uint256 amount,
  uint256 withdrawnAmount,
  uint256 feeAmount,
  uint256 projectOwnerDistributionAmount,
  string memo,
  address caller
);
```

* `fundingCycleId` is the ID of the funding cycle during which payouts were distributed.
* `projectId` is the ID of the project that had payout distributed.
* `projectOwner` is the address who owns the project and who received any leftover payouts after splits were settled.
* `amount` is the total amount that was distributed in terms of the funding cycle's currency.
* `withdrawnAmount` is the total ETH amount that was withdrawn from the project's balance.
* `feeAmount` is the total ETH amount that was paid as a fee to the protocol as a result of the distribution.
* `projectOwnerDistributionAmount` is the total ETH amount that was distributed to the project owner.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
