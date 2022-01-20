# \_packedPermissions

Contract:[`JBOperatorStore`](../)​‌

Interface: `IJBOperatorStore`

{% tabs %}
{% tab title="Step by step" %}
**Converts an array of permission indexes to a packed `uint256`.**

_Only an address can set its own operators._

## Definition

```solidity
function _packedPermissions(uint256[] calldata _indexes) private pure returns (uint256 packed) {...}
```

* `_indexes` are the indexes of the permissions to pack.
* The function is private to the contract.
* The function does not modify or reference state variables outside the function.
* The function returns the `packed` value.

## Body

1.  Loop through the provided `_indexes`.

    ```solidity
    for (uint256 _i = 0; _i < _indexes.length; _i++) { ... }
    ```
2.  Get a reference to the `_permissionIndex` being iterated on.

    ```solidity
    uint256 _index = _indexes[_i];
    ```
3.  Make sure the `_permissionIndex` is one of the 255 indexes in a `uint256`.

    ```solidity
    if (_index > 255) {
      revert PERMISSION_INDEX_OUT_OF_BOUNDS();
    }
    ```
4.  Flip the bit at the specified index of the `packed` value being returned to indicate a truthy permission.

    ```solidity
    // Turn the bit at the index on.
    packed |= 1 << _index;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Converts an array of permission indexes to a packed uint256.

  @param _indexes The indexes of the permissions to pack.

  @return packed The packed result.
*/
function _packedPermissions(uint256[] calldata _indexes) private pure returns (uint256 packed) {
  for (uint256 _i = 0; _i < _indexes.length; _i++) {
    uint256 _index = _indexes[_i];
    if (_index > 255) {
      revert PERMISSION_INDEX_OUT_OF_BOUNDS();
    }
    // Turn the bit at the index on.
    packed |= 1 << _index;
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String                               | Description                                                               |
| ------------------------------------ | ------------------------------------------------------------------------- |
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
