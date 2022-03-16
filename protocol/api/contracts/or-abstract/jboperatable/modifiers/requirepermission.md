# requirePermission

```solidity
modifier requirePermission(
  address _account,
  uint256 _domain,
  uint256 _permissionIndex
) {
  require(
    msg.sender == _account ||
      operatorStore.hasPermission(msg.sender, _account, _domain, _permissionIndex) ||
      operatorStore.hasPermission(msg.sender, _account, 0, _permissionIndex),
    'Operatable: UNAUTHORIZED'
  );
  _;
}
```