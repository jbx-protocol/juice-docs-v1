# \_packAndStoreUserPropertiesOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Efficiently stores a funding cycles provided user defined properties.**

# Definition

```solidity
function _packAndStoreUserPropertiesOf(
  uint256 _configuration,
  uint256 _projectId,
  IJBFundingCycleBallot _ballot,
  uint256 _duration,
  uint256 _discountRate
) private { ... }
```

* Arguments:
  * `_configuration` is the configuration of the funding cycle to pack and store.
  * `_projectId` is the ID of the project to which the funding cycle being packed and stored belongs.
  * `_ballot` is the ballot to use for future reconfiguration approvals.
  * `_duration` is the duration of the funding cycle.
  * `_discountRate` is the discount rate of the base funding cycle.
* The function is private to this contract.
* The function doesn't return anything.

# Body

1.  If all properties passed in are empty, there's no need to store anything.

    ```solidity
    // If all properties are zero, no need to store anything as the default value will have the same outcome.
    if (_ballot == IJBFundingCycleBallot(address(0)) && _duration == 0 && _discountRate == 0)
      return;
    ```
2.  Store the ballot in the first 160 bits of the packed `uint256`.

    ```solidity
    // ballot in bytes 0-159 bits.
    uint256 packed = uint160(address(_ballot));
    ```
3.  Store the `_duration` in the next 64 bits.

    ```solidity
    // duration in bytes 160-223 bits.
    packed |= _duration << 160;
    ```
4.  Store the `_discountRate` in the next 32 bits.

    ```solidity
    // discountRate in bytes 224-255 bits.
    packed |= _discountRate << 224;
    ```
5.  Store the packed configuration properties of the funding cycle.

    ```solidity
    // Set in storage.
    _packedUserPropertiesOf[_projectId][_configuration] = packed;
    ```

    _Internal references:_

    * [`_packedUserPropertiesOf`](../properties/\_packeduserpropertiesof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Efficiently stores a funding cycles provided user defined properties.

  @param _configuration The configuration of the funding cycle to pack and store.
  @param _projectId The ID of the project to which the funding cycle being packed and stored belongs.
  @param _ballot The ballot to use for future reconfiguration approvals. 
  @param _duration The duration of the funding cycle.
  @param _discountRate The discount rate of the base funding cycle.
*/
function _packAndStoreUserPropertiesOf(
  uint256 _configuration,
  uint256 _projectId,
  IJBFundingCycleBallot _ballot,
  uint256 _duration,
  uint256 _discountRate
) private {
  // If all properties are zero, no need to store anything as the default value will have the same outcome.
  if (_ballot == IJBFundingCycleBallot(address(0)) && _duration == 0 && _discountRate == 0)
    return;

  // ballot in bytes 0-159 bits.
  uint256 packed = uint160(address(_ballot));
  // duration in bytes 160-223 bits.
  packed |= _duration << 160;
  // discountRate in bytes 224-255 bits.
  packed |= _discountRate << 224;

  // Set in storage.
  _packedUserPropertiesOf[_projectId][_configuration] = packed;
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
