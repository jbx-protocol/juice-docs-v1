# Pay

## Pay

Emitted from:

* [`pay`](broken-reference)

# Definition

```solidity
event Pay(
  uint256 indexed fundingCycleConfiguration,
  uint256 indexed fundingCyclNumber,
  uint256 indexed projectId,
  address indexed beneficiary,
  uint256 amount,
  uint256 weight,
  uint256 tokenCount,
  string memo,
  address caller
);
```

* `fundingCycleConfiguration` is the funding cycle configuration during which the payment was made.
* `fundingCycleNumber` is the number of the funding cycle during which the payment was made.
* `projectId` is the ID of the project that was paid.
* `beneficiary` is the address that was specified to receive benefits from the payment, such as the project's tokens.
* `amount` is the amount of ETH that was paid.
* `weight` is a number that was used to determine the value of certain side effects to the payment, such as how many of the project's tokens were issued as a result.
* `tokenCount` is the amount of the project's tokens that were issued to the beneficiary as a result of the payment made.
* `memo` is a note that was attached.
* `caller` is the address that issued the transaction within which the event was emitted.
