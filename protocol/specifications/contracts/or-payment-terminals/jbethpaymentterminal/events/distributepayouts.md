# DistributePayouts

Emitted from:

* [`distributePayoutsOf`](../write/distributepayoutsof.md)

## Definition

```solidity
event DistributePayouts(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCycleNumber,
  uint256 indexed projectId,
  address beneficiary,
  uint256 amount,
  uint256 distributedAmount,
  uint256 feeAmount,
  uint256 beneficiaryDistributionAmount,
  string memo,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which payouts were distributed.
* `fundingCycleConfiguration` is the number of the funding cycle during which payouts were distributed.
* `projectId` is the ID of the project that had payout distributed.
* `beneficiary` is the address who owns the project and who received any leftover payouts after splits were settled.
* `amount` is the total amount that was distributed in terms of the funding cycle's currency.
* `distributedAmount` is the total ETH amount that was distributed from the project's balance.
* `feeAmount` is the total ETH amount that was paid as a fee to the protocol as a result of the distribution.
* `beneficiaryDistributionAmount` is the total ETH amount that was distributed to the beneficiary.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
