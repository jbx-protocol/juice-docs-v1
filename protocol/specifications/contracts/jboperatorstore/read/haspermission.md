# hasPermission

Contract:[`JBOperatorStore`](../)​‌

Interface: [`IJBOperatorStore`](../../../interfaces/ijboperatorstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Whether or not an operator has the permission to take a certain action pertaining to the specified domain.**

### Definition

```solidity
function hasPermissions(
  address _operator,
  address _account,
  uint256 _domain,
  uint256[] calldata _permissionIndexes
) external view override returns (bool) { ... }
```

* `_operator` is the operator to check
* `_account` is the account that has given out permission to the operator.
* `_domain` is the domain that the operator has been given permissions to operate.
* `_permissionIndexes` are the permission index to check for.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBOperatorStore`](../../../interfaces/ijboperatorstore.md) interface.
* The function returns a flag indicating whether the operator has the specified permission.

### Body

1.  Make sure the `_permissionIndex` is one of the 255 indexes in a `uint256`.

    ```solidity
    if (_permissionIndex > 255) revert PERMISSION_INDEX_OUT_OF_BOUNDS();
    ```
2.  Return true if the bit is flipped on for the specified permission index. Otherwise return false.

    ```solidity
    return (((permissionsOf[_operator][_account][_domain] >> _permissionIndex) & 1) == 1)
    ```

    Internal references:

    * [`permissionsOf`](../properties/permissionsof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Whether or not an operator has the permission to take a certain action pertaining to the specified domain.

  @param _operator The operator to check.
  @param _account The account that has given out permissions to the operator.
  @param _domain The domain that the operator has been given permissions to operate.
  @param _permissionIndex The permission index to check for.

  @return A flag indicating whether the operator has the specified permission.
*/
function hasPermission(
  address _operator,
  address _account,
  uint256 _domain,
  uint256 _permissionIndex
) external view override returns (bool) {
  if (_permissionIndex > 255) revert PERMISSION_INDEX_OUT_OF_BOUNDS();

  return (((permissionsOf[_operator][_account][_domain] >> _permissionIndex) & 1) == 1);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                               | Description                                                               |
| ------------------------------------ | ------------------------------------------------------------------------- |
| **`PERMISSION_INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
{% endtab %}

{% tab title="" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
