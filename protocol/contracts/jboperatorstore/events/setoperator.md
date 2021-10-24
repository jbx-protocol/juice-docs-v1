# SetOperator

Emitted from:

* [`setOperator`](../write/setoperator.md)
* [`setOperators`](../write/setoperators.md)

## Definition

```solidity
event SetOperator(
  address indexed operator,
  address indexed account,
  uint256 indexed domain,
  uint256[] permissionIndexes,
  uint256 packed
);
```

* `operator` is the address of the operator that was set.
* `account` is the account that is that now has operators set.
* `domain`  is the domain that the operator's permissions will be applied within.
* `permissionIndexes` is an array of permissions that has been given to the operator.
* `packed` is the packed version of the permission indexes.

