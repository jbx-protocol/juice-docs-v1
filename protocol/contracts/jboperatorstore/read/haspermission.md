# hasPermission

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Whether or not an operator has the permission to take a certain action pertaining to the specified domain.**

## Definition

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
* `_permissionIndexes` is a permission indexes to check for.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBOperatorStore` interface.
* The function return a flag indicating whether or not the operator has the specified permission.

## Body

1.  Make sure the `_permissionIndex` is one of the 255 indexes in a `uint256`.

    ```solidity
    require(_permissionIndex <= 255, '0x00: INDEX_OUT_OF_BOUNDS');
    ```
2.  Return true if the bit is flipped on for the specified `_permissionIndex`. Otherwise return false.

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
  @param _permissionIndex The permission indexes to check for.

  @return Whether the operator has the specified permission.
*/
function hasPermission(
  address _operator,
  address _account,
  uint256 _domain,
  uint256 _permissionIndex
) external view override returns (bool) {
  require(_permissionIndex <= 255, '0x00: INDEX_OUT_OF_BOUNDS');
  return (((permissionsOf[_operator][_account][_domain] >> _permissionIndex) & 1) == 1);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                               |
| ------------------------------- | ------------------------------------------------------------------------- |
| **`0x00: INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
{% endtab %}

{% tab title="" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
