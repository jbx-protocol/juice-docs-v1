# requirePermissionAllowingOverride

```solidity
modifier requirePermissionAllowingOverride(
  address _account,
  uint256 _domain,
  uint256 _permissionIndex,
  bool _override
) {
  require(
    _override ||
      msg.sender == _account ||
      operatorStore.hasPermission(msg.sender, _account, _domain, _permissionIndex) ||
      operatorStore.hasPermission(msg.sender, _account, 0, _permissionIndex),
    'Operatable: UNAUTHORIZED'
  );
  _;
}
```