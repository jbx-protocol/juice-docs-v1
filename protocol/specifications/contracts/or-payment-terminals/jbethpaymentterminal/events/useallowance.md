# UseAllowance

Emitted from:

* [`useAllowanceOf`](../write/useallowanceof.md)

# Definition

```solidity
event UseAllowance(
  uint256 indexed fundingCycleId,
  uint256 indexed configuration,
  uint256 indexed projectId,
  address beneficiary,
  uint256 amount,
  uint256 feeAmount,
  uint256 transferAmount,
  address caller
);
```

* `fundingCycleId` is the ID of a project's funding cycle during which an allowance was used.
* `configuration` is the configuration of a project's funding cycle during which an allowance was used.
* `projectId` is the ID of the project whose allowance was used.
* `beneficiary` is the address whose received the allowance.
* `amount` is the amount of allowance that was used in terms of the funding cycle's currency.
* `feeAmount` is the amount of ETH protocol fees that were taken from the used allowance.
* `transferAmount` is the amount of ETH that was transfered to the beneficiary.
* `caller` is the address that issued the transaction within which the event was emitted.
