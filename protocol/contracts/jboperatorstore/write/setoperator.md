# setOperator

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Sets permissions for an operator.**

_Only an address can set its own operators._

Definition:

```solidity
 function setOperator(JBOperatorData calldata _operatorData) external override { ... }
```

* `_operatorData` are the [JBOperatorData](../../../data-structures/jboperatordata.md) that specifies the params for the operator being set.
* The function can be accessed externally by anyone. 
* The function overrides a function definition from the `IJBOperatorStore` interface.
* The function doesn't return anything.



1.  Pack the provided permissions into a `uint256`. Each bit of the resulting value represents whether or not permission has been granted for that index.

    ```solidity
    // Pack the indexes into a uint256.
    uint256 _packed = _packedPermissions(_operatorData.permissionIndexes);
    ```

    Internal references:

    * [`_packedPermissions`](\_packedpermissions.md)


2.  Store the packed permissions as the `permissionsOf` the provided `_operator`, on behalf of the `msg.sender`, specifically for the provided `_domain`.

    ```solidity
    // Store the new value.
    permissionsOf[_operatorData.operator][msg.sender][_operatorData.domain] = _packed;
    ```

    _Internal references:_

    * [`permissionsOf`](../properties/permissionsof.md)


3.  Emit a `SetOperator` event with the all relevant parameters.

    ```solidity
    emit SetOperator(
      _operatorData.operator,
      msg.sender,
      _operatorData.domain,
      _operatorData.permissionIndexes,
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
  Sets permissions for an operators.

  @dev
  Only an address can set its own operators.

  @param _operatorData The data that specifies the params for the operator being set.
    @dev _operatorData.operators The operators to whom permissions will be given.
    @dev _operatorData.domains Lists the domain that each operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
    @dev _operatorData.permissionIndexes Lists the permission indexes to set for each operator. Indexes must be between 0-255. Applications can specify the significance of each index.
*/
function setOperator(JBOperatorData calldata _operatorData) external override {
  // Pack the indexes into a uint256.
  uint256 _packed = _packedPermissions(_operatorData.permissionIndexes);

  // Store the new value.
  permissionsOf[_operatorData.operator][msg.sender][_operatorData.domain] = _packed;

  emit SetOperator(
    _operatorData.operator,
    msg.sender,
    _operatorData.domain,
    _operatorData.permissionIndexes,
    _packed
  );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                               |
| ------------------------------- | ------------------------------------------------------------------------- |
| **`0x02: INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
{% endtab %}

{% tab title="Events" %}
| Name | Data |
| ---- | ---- |

| **`SetOperator`** | <ul><li><code>address indexed operator</code></li><li><code>address indexed account</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256[] permissionIndexes</code></li><li><code>uint256 packed</code></li></ul><p><a href="../events/setoperator.md">more</a></p> |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
