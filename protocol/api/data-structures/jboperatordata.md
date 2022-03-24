# JBOperatorData

# Definition

```solidity
struct JBOperatorData {
  // The address of the operator.
  address operator;
  // The domain within which the operator is being given permissions.
  // A domain of 0 is a wildcard domain, which gives an operator access to all domains.
  uint256 domain;
  // The indexes of the permissions the operator is being given.
  uint256[] permissionIndexes;
}
```