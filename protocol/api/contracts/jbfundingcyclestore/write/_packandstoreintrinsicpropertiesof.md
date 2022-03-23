# _packAndStoreIntrinsicPropertiesOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Efficiently stores a funding cycle's provided intrinsic properties.**

### Definition

```solidity
function _packAndStoreIntrinsicPropertiesOf(
  uint256 _configuration,
  uint256 _projectId,
  uint256 _number,
  uint256 _weight,
  uint256 _basedOn,
  uint256 _start
) private { ... }
```

* Arguments:
  * `_configuration` is the configuration of the funding cycle to pack and store.
  * `_projectId` is the ID of the project to which the funding cycle belongs.
  * `_number` is the number of the funding cycle.
  * `_weight` is the weight of the funding cycle.
  * `_basedOn` is the configuration of the base funding cycle.
  * `_start` is the start time of this funding cycle.
* The function is private to this contract.
* The function doesn't return anything.

### Body

1.  The weight property should take up the first 80 bits of the packed `uint256`.

    ```solidity
    // weight in bits 0-87.
    uint256 packed = _weight;
    ```
2.  The based on configuration should take up the next 56 bits.

    ```solidity
    // basedOn in bits 88-143.
    packed |= _basedOn << 88;
    ```
3.  The start should take up the next 56 bits.

    ```solidity
    // start in bits 144-199.
    packed |= _start << 144;
    ```
4.  The number should take up the last 56 bits.

    ```solidity
    // number in bits 200-255.
    packed |= _number << 200;
    ```
5.  Store the packed intrinsic properties for the funding cycle.

    ```solidity
    // Store the packed value.
    _packedIntrinsicPropertiesOf[_projectId][_configuration] = packed;
    ```

    _Internal references:_

    * [`_packedIntrinsicPropertiesOf`](../properties/_packedintrinsicpropertiesof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Efficiently stores a funding cycle's provided intrinsic properties.

  @param _configuration The configuration of the funding cycle to pack and store.
  @param _projectId The ID of the project to which the funding cycle belongs.
  @param _number The number of the funding cycle.
  @param _weight The weight of the funding cycle.
  @param _basedOn The configuration of the base funding cycle.
  @param _start The start time of this funding cycle.
*/
function _packAndStoreIntrinsicPropertiesOf(
  uint256 _configuration,
  uint256 _projectId,
  uint256 _number,
  uint256 _weight,
  uint256 _basedOn,
  uint256 _start
) private {
  // weight in bits 0-87.
  uint256 packed = _weight;

  // basedOn in bits 88-143.
  packed |= _basedOn << 88;

  // start in bits 144-199.
  packed |= _start << 144;

  // number in bits 200-255.
  packed |= _number << 200;

  // Store the packed value.
  _packedIntrinsicPropertiesOf[_projectId][_configuration] = packed;
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
