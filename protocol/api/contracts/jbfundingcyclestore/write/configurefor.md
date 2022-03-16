# configureFor

Contract:[`JBFundingCycleStore`](../)​‌

Interface: [`IJBFundingCycleStore`](../../../interfaces/ijbfundingcyclestore.md)

{% tabs %}
{% tab title="Step by step" %}
**Configures the next eligible funding cycle for the specified project.**

_Only a project's current controller can configure its funding cycles._

#### Definition

```solidity
function configureFor(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _metadata,
  uint256 _mustStartAtOrAfter
) external override onlyController(_projectId) returns (JBFundingCycle memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being configured.
  * `_data` is the [`JBFundingCycleData`](../../../data-structures/jbfundingcycledata.md) for the configuration.
  * `_metadata` is arbitrary extra data to associate with this funding cycle configuration that's not used within.
  * `_mustStartAtOrAfter` is the time before which the initialized funding cycle cannot start.
* Through the [`onlyController`](../../or-abstract/jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the [`IJBFundingCycleStore`](../../../interfaces/ijbfundingcyclestore.md) interface.
* Returns the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) that the configuration will take effect during..

#### Body

1.  Make sure the duration fits in a `uint64`.

    ```solidity
    // Duration must fit in a uint64.
    if (_data.duration > type(uint64).max) revert INVALID_DURATION();
    ```
2.  Make sure the `_data.discountRate` is less than the expected maximum value.

    ```solidity
    // Discount rate must be less than or equal to 100%.
    if (_data.discountRate > JBConstants.MAX_DISCOUNT_RATE) revert INVALID_DISCOUNT_RATE();
    ```

    _Libraries used:_

    * [`JBConstants`](../../../libraries/jbconstants.md)
      * `.MAX_DISCOUNT_RATE`
3.  Make sure the `_data.weight` fits in a `uint80`.

    ```solidity
    // Weight must fit into a uint88.
    if (_data.weight > type(uint88).max) revert INVALID_WEIGHT();
    ```
4.  Get a reference to the time at which the configuration is occurring.

    ```solidity
    // The configuration timestamp is now.
    uint256 _configuration = block.timestamp;
    ```
5.  Configure the intrinsic properties. This'll create a new funding cycle if there isn't a queued one already.

    ```solidity
    // Set up a reconfiguration by configuring intrinsic properties.
    _configureIntrinsicPropertiesFor(
      _projectId,
      _configuration,
      _data.weight,
      // Must start on or after the current timestamp.
      _mustStartAtOrAfter > block.timestamp ? _mustStartAtOrAfter : block.timestamp
    );
    ```

    _Internal references:_

    * [`_configureIntrinsicpropertiesFor`](\_configureintrinsicpropertiesfor.md)
6.  Store all of the user configuration properties provided. These properties can all be packed into one `uint256` storage slot. No need to store if the resulting stored value would be 0 since the storage slot defaults to 0.

    ```solidity
    // Efficiently stores a funding cycles provided user defined properties.
    // If all user config properties are zero, no need to store anything as the default value will have the same outcome.
    if (
      _data.ballot != IJBFundingCycleBallot(address(0)) ||
      _data.duration > 0 ||
      _data.discountRate > 0
    ) {
      // ballot in bits 0-159 bytes.
      uint256 packed = uint160(address(_data.ballot));

      // duration in bits 160-223 bytes.
      packed |= _data.duration << 160;

      // discountRate in bits 224-255 bytes.
      packed |= _data.discountRate << 224;

      // Set in storage.
      _packedUserPropertiesOf[_projectId][_configuration] = packed;
    }
    ```

    _Internal references:_

    * [`_packAndStoreUserPropertiesOf`](\_packandstoreuserpropertiesof.md)
7.  Store the provided metadata for the configuration. No need to store if the value is 0 since the storage slot defaults to 0.

    ```solidity
    // Set the metadata if needed.
    if (_metadata > 0) _metadataOf[_projectId][_configuration] = _metadata;
    ```

    _Internal references:_

    * [`_metadataOf`](../properties/\_metadataof.md)
8.  Emit a `Configure` event with the relevant parameters.

    ```solidity
    emit Configure(_configuration, _projectId, _data, _metadata, _mustStartAtOrAfter, msg.sender);
    ```

    _Event references:_

    * [`Configure`](../events/configure.md)
9.  Return the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) struct that carries the new configuration.

    ```solidity
    // Return the funding cycle for the new configuration.
    return _getStructFor(_projectId, _configuration);
    ```

    _Internal references:_

    * [`_getStructFor`](../read/\_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Configures the next eligible funding cycle for the specified project.

  @dev
  Only a project's current controller can configure its funding cycles.

  @param _projectId The ID of the project being configured.
  @param _data The funding cycle configuration data.
  @param _metadata Arbitrary extra data to associate with this funding cycle configuration that's not used within.
  @param _mustStartAtOrAfter The time before which the initialized funding cycle cannot start.

  @return The funding cycle that the configuration will take effect during.
*/
function configureFor(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _metadata,
  uint256 _mustStartAtOrAfter
) external override onlyController(_projectId) returns (JBFundingCycle memory) {
  // Duration must fit in a uint64.
  if (_data.duration > type(uint64).max) revert INVALID_DURATION();

  // Discount rate must be less than or equal to 100%.
  if (_data.discountRate > JBConstants.MAX_DISCOUNT_RATE) revert INVALID_DISCOUNT_RATE();

  // Weight must fit into a uint88.
  if (_data.weight > type(uint88).max) revert INVALID_WEIGHT();

  // The configuration timestamp is now.
  uint256 _configuration = block.timestamp;

  // Set up a reconfiguration by configuring intrinsic properties.
  _configureIntrinsicPropertiesFor(
    _projectId,
    _configuration,
    _data.weight,
    // Must start on or after the current timestamp.
    _mustStartAtOrAfter > block.timestamp ? _mustStartAtOrAfter : block.timestamp
  );

  // Efficiently stores a funding cycles provided user defined properties.
  // If all user config properties are zero, no need to store anything as the default value will have the same outcome.
  if (
    _data.ballot != IJBFundingCycleBallot(address(0)) ||
    _data.duration > 0 ||
    _data.discountRate > 0
  ) {
    // ballot in bits 0-159 bytes.
    uint256 packed = uint160(address(_data.ballot));

    // duration in bits 160-223 bytes.
    packed |= _data.duration << 160;

    // discountRate in bits 224-255 bytes.
    packed |= _data.discountRate << 224;

    // Set in storage.
    _packedUserPropertiesOf[_projectId][_configuration] = packed;
  }

  // Set the metadata if needed.
  if (_metadata > 0) _metadataOf[_projectId][_configuration] = _metadata;

  emit Configure(_configuration, _projectId, _data, _metadata, _mustStartAtOrAfter, msg.sender);

  // Return the funding cycle for the new configuration.
  return _getStructFor(_projectId, _configuration);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                      | Description                                                                  |
| --------------------------- | ---------------------------------------------------------------------------- |
| **`INVALID_DURATION`**      | Thrown if the provided duration is greater than 2^64 - 1 (1.844E19)          |
| **`INVALID_DISCOUNT_RATE`** | Thrown if the provided discount rate is greater than the max expected value. |
| **`INVALID_WEIGHT`**        | Thrown if the provided weight is greater than 2^88 - 1 (3.09E26)             |
{% endtab %}

{% tab title="Events" %}
| Name                                      | Data                                                                                                                                                                                                                                                                                                                                                                                                         |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`Configure`**](../events/configure.md) | <ul><li><code>uint256 indexed configuration</code></li><li><code>uint256 indexed projectId</code></li><code>[`JBFundingCycleData`](../../data-structures/jbfundingcycledata.md)data</code></li><li><code>uint256 metadata</code></li><li><code>uint256 mustStartAtOrAfter</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
