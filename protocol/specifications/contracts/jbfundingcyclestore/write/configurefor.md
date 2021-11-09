# configureFor

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Configures the next eligible funding cycle for the specified project.**

_Only a project's current controller can configure its funding cycles._

# Definition

```solidity
function configureFor(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _metadata
) external override onlyController(_projectId) returns (JBFundingCycle memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being configured.
  * `_data` is the [`JBFundingCycleData`](../../../data-structures/jbfundingcycledata.md)for the configuration.
  * `_metadata` is data to associate with this funding cycle configuration.
* Through the [`onlyController`](../../or-abstract/jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* Returns the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) that was configured.

# Body

1.  Make sure the `_data.duration` fits in a `uint64`. It must also be greater than 1000 seconds to prevent extractive miner behavior that is possible with timestamp based calculations within short intervals.

    ```solidity
    // Duration must fit in a uint64, and must be greater than 1000 seconds to prevent manipulative miner behavior.
    require(_data.duration <= type(uint64).max && _data.duration > 1000, '0x15: BAD_DURATION');
    ```
2.  Make sure the `_data.discountRate` is at most 201.

    ```solidity
    // Discount rate token must be less than or equal to 100%. A value of 1000000001 means non-recurring.
    require(_data.discountRate <= 1000000001, '0x16: BAD_DISCOUNT_RATE');
    ```
3.  Make sure the `_data.weight` fits in a `uint80`.

    ```solidity
    // Weight must fit into a uint88.
    require(_data.weight <= type(uint88).max, '0x18: BAD_WEIGHT');
    ```
4.  Get a reference to the time at which the configuration is occurring.

    ```solidity
    // The configuration timestamp is now.
    uint256 _configuration = block.timestamp;
    ```
5.  Configure the intrinsic properties. This'll create a new funding cycle if there isn't a queued one already.

    ```solidity
    // Set up a reconfiguration by configuring intrinsic properties.
    _configureIntrinsicProperiesFor(_projectId, _configuration, _data.weight);
    ```

    _Internal references:_

    * [`_configureIntrinsicProperiesFor`](\_configureintrinsicproperiesfor.md)
6.  Store all of the user properties provided. These properties can all be packed into one `uint256` storage slot.

    ```solidity
    // Store the configuration.
    _packAndStoreUserPropertiesOf(
      _configuration,
      _projectId,
      _data.ballot,
      _data.duration,
      _data.discountRate
    );
    ```

    _Internal references:_

    * [`_packAndStoreUserPropertiesOf`](\_packandstoreuserpropertiesof.md)
7.  Store the provided `_metadata` for the configuration.

    ```solidity
    // Set the metadata if needed.
    if (_metadata > 0) _metadataOf[_projectId][_configuration] = _metadata;
    ```

    _Internal references:_

    * [`_metadataOf`](../properties/_metadataof.md)
8.  Emit a `Configure` event with the relevant parameters.

    ```solidity
    emit Configure(_configuration, _projectId, _data, _metadata, msg.sender);
    ```

    _Event references:_

    * [`Configure`](../events/configure.md)
9. Return the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) struct that carries the new configuration.

    ```solidity
    // Return the funding cycle for the new configuration.
    return _getStructFor(_projectId, _configuration);
    ```

    _Internal references:_

    * [`_getStructFor`](../read/_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Configures the next eligible funding cycle for the specified project.

  @dev
  Only a project's current controller can configure its funding cycles.

  @param _projectId The ID of the project being configured.
  @param _data The funding cycle configuration.
    @dev _data.target The amount that the project wants to receive in each funding cycle. 18 decimals.
    @dev _data.duration The duration of the funding cycle for which the `_target` amount is needed. Measured in days. 
      Set to 0 for no expiry and to be able to reconfigure anytime.
    @dev _data.discountRate A number from 0-1000000000 indicating how valuable a contribution to this funding cycle is compared to previous funding cycles.
      If it's 0, each funding cycle will have equal weight.
      If the number is 900000000, a contribution to the next funding cycle will only give you 10% of tickets given to a contribution of the same amoutn during the current funding cycle.
      If the number is 1000000001, an non-recurring funding cycle will get made.
    @dev _data.ballot The new ballot that will be used to approve subsequent reconfigurations.
  @param _metadata Data to associate with this funding cycle configuration.

  @return The funding cycle that the configuration will take effect during.
*/
function configureFor(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _metadata
) external override onlyController(_projectId) returns (JBFundingCycle memory) {
  // Duration must fit in a uint64, and must be greater than 1000 seconds to prevent manipulative miner behavior.
  require(_data.duration <= type(uint64).max && _data.duration > 1000, '0x15: BAD_DURATION');

  // Discount rate token must be less than or equal to 100%. A value of 1000000001 means non-recurring.
  require(_data.discountRate <= 1000000001, '0x16: BAD_DISCOUNT_RATE');

  // Weight must fit into a uint88.
  require(_data.weight <= type(uint88).max, '0x18: BAD_WEIGHT');

  // The configuration timestamp is now.
  uint256 _configuration = block.timestamp;

  // Set up a reconfiguration by configuring intrinsic properties.
  _configureIntrinsicProperiesFor(_projectId, _configuration, _data.weight);

  // Store the configuration.
  _packAndStoreUserPropertiesOf(
    _configuration,
    _projectId,
    _data.ballot,
    _data.duration,
    _data.discountRate
  );

  // Set the metadata if needed.
  if (_metadata > 0) _metadataOf[_projectId][_configuration] = _metadata;

  emit Configure(_configuration, _projectId, _data, _metadata, msg.sender);

  // Return the funding cycle for the new configuration.
  return _getStructFor(_projectId, _configuration);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                        | Description                                                       |
| ----------------------------- | ----------------------------------------------------------------- |
| **`0x15: BAD_DURATION`**      | Thrown if the provided duration is greater than 2^16 - 1 (65,535) |
| **`0x16: BAD_DISCOUNT_RATE`** | Thrown if the provided discount rate is greater than 201.         |
| **`0x17: BAD_CURRENCY`**      | Thrown if the provided currency is greater than 2^8 - 1 (255)     |
| **`0x18: BAD_WEIGHT`**        | Thrown if the provided weight is greater than 2^80 - 1 (1.2E24)   |
| **`0x19: BAD_FEE`**           | Thrown if the provided fee is greater than 200.                   |
{% endtab %}

{% tab title="Events" %}
| Name                                      | Data                                                                                                                                                                                                                                                                                                                                                         |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`Configure`**](../events/configure.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed configured</code></li><li><a href="../../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>data</code></li><li><code>uint256 metadata</code></li><li><code>address caller</code></li></ul> |
| [**`Init`**](../events/init.md)           | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed basedOn</code></li></ul>                                                                                                                                                                                                    |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
