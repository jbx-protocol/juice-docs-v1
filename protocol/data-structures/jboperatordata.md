# JBOperatorData

Used in:

* [`JBOperatorStore`](../contracts/jboperatorstore/)
  * [`setOperator`](../contracts/jboperatorstore/write/setoperator.md)
  * [`setOperators`](../contracts/jboperatorstore/write/setoperators.md)

Definition:

```javascript
struct JBOperatorData {
  address operator;
  uint256 domain;
  uint256[] permissionIndexes;
}
```

* `operators` are the operator to whom permissions will be given.
* `domains` is lists the domain that each operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
* `permissionIndexes` lists the permission indexes to set for each operator. Indexes must be between 0-255. Applications can specify the significance of each index.

