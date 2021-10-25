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
  uint256 _metadata,
  uint256 _fee,
  bool _configureActiveFundingCycle
) external override onlyController(_projectId) returns (JBFundingCycle memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being configured.
  * `_data` is the [`JBFundingCycleData`](../../../data-structures/jbfundingcycledata.md)for the configuration.
  * `_metadata` is data to associate with this funding cycle configuration.
  * `_fee` is the fee that this configuration incurs when tapping.
  * `_configureActiveFundingCycle` is a flag indicating if a funding cycle that has already started should be configurable.
* Through the [`onlyController`](../../jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* Returns the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) that was configured.

# Body

1.  Make sure the `_data.duration` fits in a `uint16`.

    ```solidity
    // Duration must fit in a uint16.
    require(_data.duration <= type(uint16).max, '0x15: BAD_DURATION');
    ```
2.  Make sure the `_data.discountRate` is at most 201.

    ```solidity
    // Discount rate token must be less than or equal to 100%. A value of 10001 means non-recurring.
    require(_data.discountRate <= 10001, '0x16: BAD_DISCOUNT_RATE');
    ```
3.  Make sure the `_data.currency` fits in a `uint8`.

    ```solidity
    // Currency must fit into a uint8.
    require(_data.currency <= type(uint8).max, '0x17: BAD_CURRENCY');
    ```
4.  Make sure the `_data.weight` fits in a `uint80`.

    ```solidity
    // Weight must fit into a uint8.
    require(_data.weight <= type(uint80).max, '0x18: BAD_WEIGHT');
    ```
5.  Make sure the provided `_fee` is at most 200.

    ```solidity
    // Fee must be less than or equal to 100%.
    require(_fee <= 200, '0x19: BAD_FEE');
    ```
6.  Get a reference to the time at which the configuration is occurring.

    ```solidity
    // Set the configuration timestamp is now.
    uint256 _configured = block.timestamp;
    ```
7.  Find the ID of the funding cycle that should be configured.

    ```solidity
    // Gets the ID of the funding cycle to reconfigure.
    uint256 _fundingCycleId = _configurableOf(
      _projectId,
      _configured,
      _data.weight,
      _configureActiveFundingCycle
    );
    ```

    _Internal references:_

    * [`_configurableOf`](\_configurableof.md)
8.  Store all of the configuration properties provided onto the `_fundingCycleId`. These properties can all be packed into one `uint256` storage slot.

    ```solidity
    // Store the configuration.
    _packAndStoreConfigurationPropertiesOf(
      _fundingCycleId,
      _configured,
      _data.ballot,
      _data.duration,
      _data.currency,
      _fee,
      _data.discountRate
    );
    ```

    _Internal references:_

    * [`_packAndStoreConfigurationPropertiesOf`](\_packandstoreconfigurationpropertiesof.md)
9.  Store the provided `_data.target` for the `_fundingCycleId`.

    ```solidity
    // Set the target amount.
    _targetOf[_fundingCycleId] = _data.target;
    ```

    _Internal references:_

    * [`_targetOf`](../properties/\_targetof.md)
10. Store the provided `_metadata` for the `_fundingCycleId`.

    ```solidity
    // Set the metadata.
    _metadataOf[_fundingCycleId] = _metadata;
    ```

    _Internal references:_

    * [`_metadataOf`](../properties/\_metadataof.md)
11. Emit a `Configure` event with the all relevant parameters.

    ```solidity
    emit Configure(_fundingCycleId, _projectId, _configured, _data, _metadata, msg.sender);
    ```

    _Event references:_

    * [`Configure`](../events/configure.md)
12. Return the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) struct that carries the new configuration.

    ```solidity
    return _getStructFor(_fundingCycleId);
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
  @param _data The funding cycle configuration.
    @dev _data.target The amount that the project wants to receive in each funding cycle. 18 decimals.
    @dev _data.currency The currency of the `_target`. Send 0 for ETH or 1 for USD.
    @dev _data.duration The duration of the funding cycle for which the `_target` amount is needed. Measured in days. 
      Set to 0 for no expiry and to be able to reconfigure anytime.
    @dev _data.discountRate A number from 0-10000 indicating how valuable a contribution to this funding cycle is compared to previous funding cycles.
      If it's 0, each funding cycle will have equal weight.
      If the number is 9000, a contribution to the next funding cycle will only give you 10% of tickets given to a contribution of the same amoutn during the current funding cycle.
      If the number is 10001, an non-recurring funding cycle will get made.
    @dev _data.ballot The new ballot that will be used to approve subsequent reconfigurations.
  @param _metadata Data to associate with this funding cycle configuration.
  @param _fee The fee that this configuration incurs when tapping.
  @param _configureActiveFundingCycle A flag indicating if a funding cycle that has already started should be configurable.

  @return The funding cycle that the configuration will take effect during.
*/
function configureFor(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _metadata,
  uint256 _fee,
  bool _configureActiveFundingCycle
) external override onlyController(_projectId) returns (JBFundingCycle memory) {
  // Duration must fit in a uint16.
  require(_data.duration <= type(uint16).max, '0x15: BAD_DURATION');

  // Discount rate token must be less than or equal to 100%. A value of 201 means non-recurring.
  require(_data.discountRate <= 201, '0x17: BAD_DISCOUNT_RATE');

  // Currency must fit into a uint8.
  require(_data.currency <= type(uint8).max, '0x18: BAD_CURRENCY');

  // Weight must fit into a uint8.
  require(_data.weight <= type(uint80).max, '0x19: BAD_WEIGHT');

  // Fee must be less than or equal to 100%.
  require(_fee <= 200, '0x1a: BAD_FEE');

  // Set the configuration timestamp is now.
  uint256 _configured = block.timestamp;

  // Gets the ID of the funding cycle to reconfigure.
  uint256 _fundingCycleId = _configurableOf(
    _projectId,
    _configured,
    _data.weight,
    _configureActiveFundingCycle
  );

  // Store the configuration.
  _packAndStoreConfigurationPropertiesOf(
    _fundingCycleId,
    _configured,
    _data.ballot,
    _data.duration,
    _data.currency,
    _fee,
    _data.discountRate
  );

  // Set the target amount.
  _targetOf[_fundingCycleId] = _data.target;

  // Set the metadata.
  _metadataOf[_fundingCycleId] = _metadata;

  emit Configure(_fundingCycleId, _projectId, _configured, _data, _metadata, msg.sender);

  return _getStructFor(_fundingCycleId);
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
