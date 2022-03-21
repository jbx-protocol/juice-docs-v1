# DistributeReservedTokens

Emitted from:

* [`distributeReservedTokensOf`](../write/distributereservedtokensof.md)

## Definition

```solidity
event DistributeReservedTokens(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCycleNumber,
  uint256 indexed projectId,
  address beneficiary,
  uint256 tokenCount,
  uint256 beneficiaryTokenCount,
  string memo,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which the reserved tokens were distributed.
* `fundingCycleNumber` is the number of the funding cycle during which the reserved tokens were distributed.
* `projectId` is the ID of the token's project.
* `beneficiary` is the address that received any leftover tokens after splits were applied.
* `tokenCount` is the total number of tokens that were distributed.
* `beneficiaryTokenCount` is the number of tokens that were distributed to the beneficiary.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
