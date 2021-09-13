# setOperator

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Sets permissions for an operator.**

_Only an address can set its own operators._  
  
Definition:

```javascript
function setOperator(
    address _operator,
    uint256 _domain,
    uint256[] calldata _permissionIndexes
) external override { ... }
```

* `_operator` is the operator to whom permissions will be given.
* `_domain` is the domain that the operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
* `_permissionIndexes` is an array of permission indexes to set. Indexes must be between 0-255. Applications can specify the significance of each index.
* The function can be accessed externally by anyone. 
* The function overrides a function definition from the `IJBOperatorStore` interface.
* The function doesn't return anything.

1. Pack the provided permissions into a `uint256`. Each bit of the resulting value represents whether or not permission has been granted for that index.  


   _Internal references:_

   * [`_packedPermissions`](_packedpermissions.md)

   ```javascript
   // Pack the indexes into a uint256.
   uint256 _packed = _packedPermissions(_permissionIndexes);
   ```

   2. Store the packed permissions as the `permissionsOf` the provided `_operator`, on behalf of the `msg.sender`, specifically for the provided `_domain`.  


   _Internal references:_

   * [`permissionsOf`](../read/permissionsof.md)

   ```javascript
   // Store the new value.
   permissionsOf[_operator][msg.sender][_domain] = _packed;
   ```

   3. Emit a `SetOperator` event with the all relevant parameters.   


   _Event references:_

   * [`SetOperator`](../events/setoperator.md)

   ```javascript
   emit SetOperator(
       _operator,
       msg.sender,
       _domain,
       _permissionIndexes,
       _packed
   );
   ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Sets permissions for an operator.

  @param _operator The operator to whom permissions will be given.
  @param _domain The domain that the operator is being given permissions to operate. A value of 0 serves as a wildcard domain. Applications can specify their own domain system.
  @param _permissionIndexes An array of permission indexes to set. Indexes must be between 0-255. Applications can specify the significance of each index.
*/
function setOperator(
    address _operator,
    uint256 _domain,
    uint256[] calldata _permissionIndexes
) external override {
    // Pack the indexes into a uint256.
    uint256 _packed = _packedPermissions(_permissionIndexes);

    // Store the new value.
    permissionsOf[_operator][msg.sender][_domain] = _packed;

    emit SetOperator(
        _operator,
        msg.sender,
        _domain,
        _permissionIndexes,
        _packed
    );
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBOperatorStore::_packedPermissions: INDEX_OUT_OF_BOUNDS`** | Thrown if the provided index is more than whats supported in a `uint256`. |
{% endtab %}

{% tab title="Events" %}
<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>SetOperator</code></b>
      </td>
      <td style="text-align:left">
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
      </td>
    </tr>
  </tbody>
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

