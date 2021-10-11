# permissionsOf

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

**The permissions that an operator has to operate on a specific domain.**

_An account can give an operator permissions that only pertain to a specific domain. There is no domain with a value of 0 – accounts can use the 0 domain to give an operator permissions to all domains on their behalf. Applications can specify their own domain system._

_Permissions are stored in a packed `uint256`. Each 256 bits represents the on/off state of a permission. Applications can specify the significance of each index._\
__\
__Definition:

```javascript
/** 
  @notice
  The permissions that an operator has to operate on a specific domain.
    
  @dev
  An account can give an operator permissions that only pertain to a specific domain.
  There is no domain with a value of 0 – accounts can use the 0 domain to give an operator
  permissions to all domains on their behalf.

  [_operator][_account][_domain]
*/
mapping(address => mapping(address => mapping(uint256 => uint256))) public override permissionsOf;
```

* `address` is the address of the operator.
* `address` is the address of the account being operated.
* `uint256` is the domain within which the permissions apply.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBOperatorStore` interface.
