# setOperators

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Sets permissions for many operators.**

_Only an address can set its own operators._

Definition:

```javascript
 function setOperators(OperatorData[] calldata _operatorData) external override { ... }
```

* `_operatorData` are the data that specifies the params for each operator being set.
  * `_operatorData.operators` are the operator to whom permissions will be given.
  * `_operatorData.domains` is lists the domain that each operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
  * `_operatorData.permissionIndexes` lists the permission indexes to set for each operator. Indexes must be between 0-255. Applications can specify the significance of each index.
* The function can be accessed externally by anyone. 
* The function overrides a function definition from the `IJBOperatorStore` interface.
* The function doesn't return anything.

1. Loop through the provided `_operatorData`.

   ```javascript
   for (uint256 _i = 0; _i < _operatorData.length; _i++) { ... }
   ```

2. Pack the provided permissions into a `uint256`. Each bit of the resulting value represents whether or not permission has been granted for that index.    


   Internal references:

   * [`_packedPermissions`](_packedpermissions.md)

   ```javascript
   // Pack the indexes into a uint256.
   uint256 _packed = _packedPermissions(_operatorData[_i].permissionIndexes);
   ```

3. Store the packed permissions as the `permissionsOf` the provided `_operator`, on behalf of the `msg.sender`, specifically for the provided `_domain`.    


   _Internal references:_

   * [`permissionsOf`](../properties/permissionsof.md)

   ```javascript
   // Store the new value.
   permissionsOf[_operatorData[_i].operator][msg.sender][_operatorData[_i].domain] = _packed;
   ```

4. Emit a `SetOperator` event with the all relevant parameters.     


   _Event references:_

   * [`SetOperator`](../events/setoperator.md)

   ```javascript
   emit SetOperator(
     _operatorData[_i].operator,
     msg.sender,
     _operatorData[_i].domain,
     _operatorData[_i].permissionIndexes,
     _packed
   );
   ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice
  Sets permissions for many operators.

  @dev
  Only an address can set its own operators.

  @param _operatorData The data that specifies the params for each operator being set.
    @dev _operators The operators to whom permissions will be given.
    @dev _domains Lists the domain that each operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
    @dev _permissionIndexes Lists the permission indexes to set for each operator. Indexes must be between 0-255. Applications can specify the significance of each index.
*/
function setOperators(OperatorData[] calldata _operatorData) external override {
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

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`0x02: INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
{% endtab %}

{% tab title="Events" %}
| Name | Data |
| :--- | :--- |


<table>
  <thead>
    <tr>
      <th style="text-align:left"><b><code>SetOperator</code></b>
      </th>
      <th style="text-align:left">
        <ul>
          <li><code>address indexed operator</code>
          </li>
          <li><code>address indexed account</code>
          </li>
          <li><code>uint256 indexed domain</code>
          </li>
          <li><code>uint256[] permissionIndexes</code>
          </li>
          <li><code>uint256 packed</code>
          </li>
        </ul>
        <p><a href="../events/setoperator.md">more</a>
        </p>
      </th>
    </tr>
  </thead>
  <tbody></tbody>
</table>
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}

