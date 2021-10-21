# _packAndStoreIntrinsicPropertiesOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Efficiently stores a funding cycle's provided intrinsic properties.**

## Definition

```solidity
function _packAndStoreIntrinsicPropertiesOf(
  uint256 _projectId,
  uint256 _number,
  uint256 _weight,
  uint256 _basedOn,
  uint256 _start
) private returns (uint256 fundingCycleId) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funding cycle belongs.
  * `_number` is the number of the funding cycle.
  * `_weight` is the weight of the funding cycle.
  * `_basedOn` is the ID of the based funding cycle.
  * `_start` is the start time of this funding cycle.
* The function is private to this contract.
* The function returns the ID of the funding cycle whose intrinsic properties have been packed and stored.

## Body

1.  The `_weight` property should take up the first 80 bits of the packed `uint256`.

    ```solidity
    // weight in bytes 0-79 bits.
    uint256 packed = _weight;
    ```
2.  The `_projectId` should take up the next 56 bits.

    ```solidity
    // projectId in bytes 80-135 bytes.
    packed |= _projectId << 80;
    ```
3.  The `_basedOn` should take up the next 48 bits.

    ```solidity
    // basedOn in bytes 136-183 bytes.
    packed |= _basedOn << 136;
    ```
4.  The `_start` should take up the next 48 bits.

    ```solidity
    // start in bytes 184-231 bytes.
    packed |= _start << 184;
    ```
5.  The `_number` should take up the last 24 bits.

    ```solidity
    // number in bytes 232-255 bytes.
    packed |= _number << 232;
    ```
6.  Derive the ID from the `_projectId` and the `_number`.

    ```solidity
    // Construct the ID.
    fundingCycleId = _idFor(_projectId, _number);
    ```

    _Internal references:_

    * [`_idFor`](../read/\_idfor.md)
7.  Store the packed intrinsic properties for the funding cycle.

    ```solidity
    // Set in storage.
    _packedIntrinsicPropertiesOf[fundingCycleId] = packed;
    ```

    _Internal references:_

    * [`_packAndStoreIntrinsicPropertiesOf`](\_packandstoreintrinsicpropertiesof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Efficiently stores a funding cycle's provided intrinsic properties.

  @param _projectId The ID of the project to which the funding cycle belongs.
  @param _number The number of the funding cycle.
  @param _weight The weight of the funding cycle.
  @param _basedOn The ID of the based funding cycle.
  @param _start The start time of this funding cycle.

  @return fundingCycleId The ID of the funding cycle that was updated.
*/
function _packAndStoreIntrinsicPropertiesOf(
  uint256 _projectId,
  uint256 _number,
  uint256 _weight,
  uint256 _basedOn,
  uint256 _start
) private returns (uint256 fundingCycleId) {
  // weight in bytes 0-79 bytes.
  uint256 packed = _weight;
  // projectId in bytes 80-135 bytes.
  packed |= _projectId << 80;
  // basedOn in bytes 136-183 bytes.
  packed |= _basedOn << 136;
  // start in bytes 184-231 bytes.
  packed |= _start << 184;
  // number in bytes 232-255 bytes.
  packed |= _number << 232;

  // Construct the ID.
  fundingCycleId = _idFor(_projectId, _number);

  // Set in storage.
  _packedIntrinsicPropertiesOf[fundingCycleId] = packed;
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
