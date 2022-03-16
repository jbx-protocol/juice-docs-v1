# setOperators

Contract:[`JBOperatorStore`](../)​‌

Interface: [`IJBOperatorStore`](../../../interfaces/ijboperatorstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Sets permissions for many operators.**

_Only an address can set its own operators._

### Definition

```solidity
function setOperators(JBOperatorData[] calldata _operatorData) external override { ... }
```

* `_operatorData` are the [JBOperatorData](../../../data-structures/jboperatordata.md) that specify the params for each operator being set.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the [`IJBOperatorStore`](../../../interfaces/ijboperatorstore.md) interface.
* The function doesn't return anything.

### Body

1.  Loop through the provided operator data.

    ```solidity
    for (uint256 _i = 0; _i < _operatorData.length; _i++) { ... }
    ```

    1.  Pack the provided permissions into a `uint256`. Each bit of the resulting value represents whether or not permission has been granted for that index.

        ```solidity
        // Pack the indexes into a uint256.
        uint256 _packed = _packedPermissions(_operatorData[_i].permissionIndexes);
        ```

        Internal references:

        * [`_packedPermissions`](../read/_packedpermissions.md)
    2.  Store the packed permissions as the permissions of the provided operator, on behalf of the `msg.sender`, specifically for the provided domain.

        ```solidity
        // Store the new value.
        permissionsOf[_operatorData[_i].operator][msg.sender][_operatorData[_i].domain] = _packed;
        ```

        _Internal references:_

        * [`permissionsOf`](../properties/permissionsof.md)
    3.  Emit a `SetOperator` event with the relevant parameters.

        ```solidity
        emit SetOperator(
          _operatorData[_i].operator,
          msg.sender,
          _operatorData[_i].domain,
          _operatorData[_i].permissionIndexes,
          _packed
        );
        ```

        _Event references:_

        * [`SetOperator`](../events/setoperator.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Sets permissions for many operators.

  @dev
  Only an address can set its own operators.

  @param _operatorData The data that specify the params for each operator being set.
*/
function setOperators(JBOperatorData[] calldata _operatorData) external override {
  for (uint256 _i = 0; _i < _operatorData.length; _i++) {
    // Pack the indexes into a uint256.
    uint256 _packed = _packedPermissions(_operatorData[_i].permissionIndexes);

    // Store the new value.
    permissionsOf[_operatorData[_i].operator][msg.sender][_operatorData[_i].domain] = _packed;

    emit SetOperator(
      _operatorData[_i].operator,
      msg.sender,
      _operatorData[_i].domain,
      _operatorData[_i].permissionIndexes,
      _packed
    );
  }
}
```
{% endtab %}

{% tab title="Events" %}
|                                               |                                                                                                                                                                                                                                       |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetOperator`**](../events/setoperator.md) | <ul><li><code>address indexed operator</code></li><li><code>address indexed account</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256[] permissionIndexes</code></li><li><code>uint256 packed</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
