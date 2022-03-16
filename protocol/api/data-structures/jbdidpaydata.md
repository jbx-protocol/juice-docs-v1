# JBDidPayData

```solidity
struct JBDidPayData {
  // The address from which the payment originated.
  address payer;
  // The ID of the project for which the payment was made.
  uint256 projectId;
  // The amount of ETH that was paid.
  uint256 amount;
  // The weight that was used for minting tokens.
  uint256 weight;
  // The number of tokens minted.
  uint256 tokenCount;
  // The address to which the tokens were minted.
  address beneficiary;
  // The memo that is being emitted alongside the payment.
  string memo;
  // Metadata to send to the delegate.
  bytes delegateMetadata;
}
```
