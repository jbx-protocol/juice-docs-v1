# setOperators

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Sets permissions for many operators.**

\_Only an address can set its own operators.

Each element of each provided array should matches up, so each array must be of the same length.\_

Definition:

```javascript
function setOperators(
    address[] calldata _operators,
    uint256[] calldata _domains,
    uint256[][] calldata _permissionIndexes
) external override { ... }
```

* `_operators` are the operator to whom permissions will be given.
* `_domains` is lists the domain that each operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
* `_permissionIndexes` lists the permission indexes to set for each operator. Indexes must be between 0-255. Applications can specify the significance of each index.
* The function can be accessed externally by anyone. 
* The function overrides a function definition from the `IJBOperatorStore` interface.
* The function doesn't return anything.

1. Make sure the array lengths of `_operators`, `_domains` and `_permissionIndexes` are the same.

   ```javascript
   // There should be a level for each operator provided.
   require(
       _operators.length == _permissionIndexes.length &&
           _operators.length == _domains.length,
       "OperatorStore::setOperators: BAD_ARGS"
   );
   ```

2. Loop through the provided `_operators`.

   ```javascript
   for (uint256 _i = 0; _i < _operators.length; _i++) { ... }
   ```

3. Pack the provided permissions into a `uint256`. Each bit of the resulting value represents whether or not permission has been granted for that index.    


   Internal references:

   * [`_packedPermissions`](_packedpermissions.md)

   ```javascript
   // Pack the indexes into a uint256.
   uint256 _packed = _packedPermissions(_permissionIndexes[_i]);
   ```

4. Store the packed permissions as the `permissionsOf` the provided `_operator`, on behalf of the `msg.sender`, specifically for the provided `_domain`.    


   _Internal references:_

   * [`permissionsOf`](../properties/permissionsof.md)

   ```javascript
   // Store the new value.
   permissionsOf[_operators[_i]][msg.sender][_domains[_i]] = _packed;
   ```

5. Emit a `SetOperator` event with the all relevant parameters.     


   _Event references:_

   * [`SetOperator`](../events/setoperator.md)

   ```javascript
   emit SetOperator(
       _operators[_i],
       msg.sender,
       _domains[_i],
       _permissionIndexes[_i],
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

  @dev
  Each element of each provided array should matches up, so each array must be of the same length. 

  @param _operators The operators to whom permissions will be given.
  @param _domains Lists the domain that each operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
  @param _permissionIndexes Lists the permission indexes to set for each operator. Indexes must be between 0-255. Applications can specify the significance of each index.
*/
function setOperators(
    address[] calldata _operators,
    uint256[] calldata _domains,
    uint256[][] calldata _permissionIndexes
) external override {
    // There should be a level for each operator provided.
    require(
        _operators.length == _permissionIndexes.length &&
            _operators.length == _domains.length,
        "OperatorStore::setOperators: BAD_ARGS"
    );

    for (uint256 _i = 0; _i < _operators.length; _i++) {
        // Pack the indexes into a uint256.
        uint256 _packed = _packedPermissions(_permissionIndexes[_i]);

        // Store the new value.
        permissionsOf[_operators[_i]][msg.sender][_domains[_i]] = _packed;

        emit SetOperator(
            _operators[_i],
            msg.sender,
            _domains[_i],
            _permissionIndexes[_i],
            _packed
        );
    }
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBOperatorStore::setOperators: BAD_ARGS`** | Thrown if the arrays passed in are not of the same length. |
| **`JBOperatorStore::_packedPermissions: INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
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

