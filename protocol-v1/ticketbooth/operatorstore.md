# OperatorStore

### Constructor

### Read

```javascript
/** 
  @notice 
  Whether or not an operator has the permission to take a certain action pertaining to the specified domain.

  @param _operator The operator to check.
  @param _account The account that has given out permission to the operator.
  @param _domain The domain that the operator has been given permissions to operate.
  @param _permissionIndex the permission to check for.

  @return Whether the operator has the specified permission.
*/
function hasPermission(
    address _operator,
    address _account,
    uint256 _domain,
    uint256 _permissionIndex
) external view override returns (bool)
```

```javascript
/** 
  @notice 
  Whether or not an operator has the permission to take certain actions pertaining to the specified domain.

  @param _operator The operator to check.
  @param _account The account that has given out permissions to the operator.
  @param _domain The domain that the operator has been given permissions to operate.
  @param _permissionIndexes An array of permission indexes to check for.

  @return Whether the operator has all specified permissions.
*/
function hasPermissions(
    address _operator,
    address _account,
    uint256 _domain,
    uint256[] calldata _permissionIndexes
) external view override returns (bool)
```

### Write

```javascript
/** 
  @notice 
  Sets permissions for an operator.

  @param _operator The operator to give permission to.
  @param _domain The domain that the operator is being given permissions to operate.
  @param _permissionIndexes An array of indexes of permissions to set.
*/
function setOperator(
    address _operator,
    uint256 _domain,
    uint256[] calldata _permissionIndexes
) external override
```

```javascript
/** 
  @notice 
  Sets permissions for many operators.

  @param _operators The operators to give permission to.
  @param _domains The domains that can be operated. Set to 0 to allow operation of account level actions.
  @param _permissionIndexes The level of power each operator should have.
*/
function setOperators(
    address[] calldata _operators,
    uint256[] calldata _domains,
    uint256[][] calldata _permissionIndexes
) external override
```
