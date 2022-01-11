# hasPermissions

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Whether or not an operator has the permission to take certain actions pertaining to the specified domain.**

# Definition

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
* `_permissionIndexes` is an array of permission indexes to check for.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBOperatorStore` interface.
* The function return a flag indicating whether or not the operator has the permissions.

# Body

1.  Loop through the provided `_permissionIndexes`.

    ```solidity
    for (uint256 _i = 0; _i < _permissionIndexes.length; _i++) { ... }
    ```
2.  Get a reference to the `_permissionIndex` being iterated on.

    ```solidity
    uint256 _permissionIndex = _permissionIndexes[_i];
    ```
3.  Make sure the `_permissionIndex` is one of the 255 indexes in a `uint256`.

    ```solidity
    if (_permissionIndex > 255) {
      revert PERMISSION_INDEX_OUT_OF_BOUNDS();
    }
    ```
4.  If the bit at the specified permission index of the `permissionsOf` the `_operator` for the specified `_account` and within the specified `_domain` is off, return `false` because all provided permissions are not on.

    ```solidity
    if (((permissionsOf[_operator][_account][_domain] >> _permissionIndex) & 1) == 0)
      return false;
    ```

    Internal references:

    * [`permissionsOf`](../properties/permissionsof.md)
5.  After the loop, return `true` since the loop checked all specified permissions without returning `false`.

    ```solidity
    return true;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
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
) external view override returns (bool) {
  for (uint256 _i = 0; _i < _permissionIndexes.length; _i++) {
    uint256 _permissionIndex = _permissionIndexes[_i];

    if (_permissionIndex > 255) {
      revert PERMISSION_INDEX_OUT_OF_BOUNDS();
    }

    if (((permissionsOf[_operator][_account][_domain] >> _permissionIndex) & 1) == 0)
      return false;
  }
  return true;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                               |
| ------------------------------- | ------------------------------------------------------------------------- |
| **`PERMISSION_INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
