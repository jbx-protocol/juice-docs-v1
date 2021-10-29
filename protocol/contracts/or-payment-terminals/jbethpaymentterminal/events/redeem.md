# Redeem

Emitted from:

* [`redeemTokensOf`](../write/redeemtokensof.md)

## Definition

```solidity
event Redeem(
  uint256 indexed fundingCycleId,
  uint256 indexed projectId,
  address indexed holder,
  JBFundingCycle fundingCycle,
  address beneficiary,
  uint256 tokenCount,
  uint256 claimedAmount,
  string memo,
  address caller
);
```

* `fundingCycleId` is the ID of a project's funding cycle during which its tokens were redeemed.
* `projectId` is the ID of the project whose tokens were redeemed.
* `holder` is the address whose tokens were redeemed.
* `fundingCycle` is the funding cycle during which tke tokens' were redeemed.
* `beneficiary` is the address to which any redemption benefits were sent, such as treasury funds.
* `tokenCount` is the amount of tokens that were redeemed.
* `claimedAmount` is the amount of ETH that were sent to the beneficiary as a result of the redemption.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
