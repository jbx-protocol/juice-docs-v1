# RedeemTokens

Emitted from:

* [`redeemTokensOf`](../write/redeemtokensof.md)

# Definition

```solidity
event RedeemTokens(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCycleNumber,
  uint256 indexed projectId,
  address indexed holder,
  address beneficiary,
  uint256 tokenCount,
  uint256 claimedAmount,
  string memo,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which tokens were redeemed.
* `fundingCycleNumber` is the number of the funding cycle during which tokens were redeemed.
* `projectId` is the ID of the project whose tokens were redeemed.
* `holder` is the address whose tokens were redeemed.
* `beneficiary` is the address to which any redemption benefits were sent, such as treasury funds.
* `tokenCount` is the amount of tokens that were redeemed.
* `claimedAmount` is the amount of ETH that were sent to the beneficiary as a result of the redemption.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
