# \_configureIntrinsicProperiesFor

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Updates the configurable funding cycle for this project if it exists, otherwise creates one.**

# Definition

```solidity
function _configureIntrinsicProperiesFor(
  uint256 _projectId,
  uint256 _configuration,
  uint256 _weight
) private { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to find a configurable funding cycle for.
  * `_configuration` is the time at which the configuration is occurring.
  * `_weight` is the weight to store in the configured funding cycle.
* The function is private to this contract.
* The function returns the ID of a configurable funding cycle.

# Body

1.  If the project does not yet have a funding cycle, initialize a new one with no base.

    ```solidity
    // If there's not yet a funding cycle for the project, initialize one.
    if (latestConfigurationOf[_projectId] == 0) {
      _initFor(_projectId, _getStructFor(0, 0), _configuration, block.timestamp, _weight);
      return;
    }
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
    * [`_initFor`](\_initfor.md)
    * [`_getStructFor`](../read/_getstructfor.md)
2.  Get a reference to the project's standby funding cycle.

    ```solidity
    // Get the standby funding cycle's configuration.
    uint256 _currentConfiguration = _standbyOf(_projectId);
    ```

    _Internal references:_

    * [`_standbyOf`](../read/_standbyof.md)
3.  If there is a standby funding cycle, return it after updating it. It must be updated because there's a chance the new reconfiguration will have a new start time than the previous reconfiguration that was in standby, depending on the specifications of the `_baseFundingCycle`'s ballot.

    ```solidity
    // If it exists, make sure its updated, then return it.
    if (_currentConfiguration > 0) {
      // Get the funding cycle that the specified one is based on.
      JBFundingCycle memory _baseFundingCycle = _getStructFor(
        _projectId,
        _getStructFor(_projectId, _currentConfiguration).basedOn
      );

      // Update the funding cycle to make sure the base's ballot has been approved.
      _updateAndStoreIntrinsicPropertiesOf(
        _configuration,
        _projectId,
        _baseFundingCycle,
        _getLatestTimeAfterBallotOf(_baseFundingCycle, _configuration),
        _weight
      );

      return;
    }
    ```

    _Internal references:_

    * [`_standbyOf`](../read/_standbyof.md)
    * [`_getStructFor`](../read/_getstructfor.md)
    * [`_updateAndStoreIntrinsicPropertiesOf`](\_updateandstoreintrinsicpropertiesof.md)
    * [`_getLatestTimeAfterBallotOf`](../read/_getlatesttimeafterballotof.md)
4.  If there's no standby funding cycle, get a reference to the project's eligible funding cycle. The configurable funding cycle will have to be initialized based on the eligible cycle.

    ```solidity
    // Get the active funding cycle's configuration.
    _currentConfiguration = _eligibleOf(_projectId);
    ```

    _Internal references:_

    * [`_eligibleOf`](../read/_eligibleof.md)
5.  If there is no eligible funding cycle for the project, get a reference instead to the project's latest funding cycle configuration, which may have been initialized long into the past. 

    ```solidity
    // If an eligible funding cycle does not exist, get a reference to the latest funding cycle configuration for the project. 
    if (_currentConfiguration == 0)
      // Get the latest funding cycle's configuration.
      _currentConfiguration = latestConfigurationOf[_projectId];
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)

6.  If the configuration isn't approves, get a reference to the configuration it's based on which must be the latest approved configuration. 

    ```solidity
    if (!_isConfigurationApproved(_projectId, _currentConfiguration))
      // If it hasn't been approved, set the ID to be the based funding cycle,
      // which carries the last approved configuration.
      _currentConfiguration = _getStructFor(_projectId, _currentConfiguration).basedOn;
    ```

    _Internal references:_

    * [`_isConfigurationApproved`](../read/_isconfigurationapproved.md)
    * [`_getStructFor`](../read/_getstructfor.md)
6.  At this point, the `_currentConfiguration` is the funding cycle configuration that the one that'll the initialized one should be based on. Get a reference to the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md)for the configuration.

    ```solidity
    // Get the funding cycle for the configuration.
    JBFundingCycle memory _currentFundingCycle = _getStructFor(_projectId, _currentConfiguration);
    ```

    _Internal references:_

    * [`_getStructFor`](../read/_getstructfor.md)
7.  Make sure the cycle is recurring, otherwise throw an error since a new funding cycle cannot be created based on a non-recurring cycle.

    ```solidity
    // Make sure the funding cycle is recurring.
    require(_currentFundingCycle.discountRate < 1000000001, '0x1c: NON_RECURRING');
    ```
8.  The funding cycle that will be initialized can start any time after the base funding cycle's ballot's duration is up.

    ```solidity
    // Determine if the configurable funding cycle can only take effect on or after a certain date.
    // The ballot must have ended.
    uint256 _mustStartOnOrAfter = _getLatestTimeAfterBallotOf(_currentFundingCycle, _configuration);
    ```

    _Internal references:_

    * [`_getLatestTimeAfterBallotOf`](../read/_getlatesttimeafterballotof.md)

9. Initialize a funding cycle with the correct configuration.

    ```solidity
    // Initialize a funding cycle.
    _initFor(_projectId, _currentFundingCycle, _configuration, _mustStartOnOrAfter, _weight);
    ```

    _Internal references:_

    * [`_initFor`](../read/_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Updates the configurable funding cycle for this project if it exists, otherwise creates one.

  @param _projectId The ID of the project to find a configurable funding cycle for.
  @param _configuration The time at which the configuration is occurring.
  @param _weight The weight to store in the configured funding cycle.
*/
function _configureIntrinsicProperiesFor(
  uint256 _projectId,
  uint256 _configuration,
  uint256 _weight
) private {
  // If there's not yet a funding cycle for the project, initialize one.
  if (latestConfigurationOf[_projectId] == 0) {
    _initFor(_projectId, _getStructFor(0, 0), _configuration, block.timestamp, _weight);
    return;
  }

  // Get the standby funding cycle's configuration.
  uint256 _currentConfiguration = _standbyOf(_projectId);

  // If it exists, make sure its updated, then return it.
  if (_currentConfiguration > 0) {
    // Get the funding cycle that the specified one is based on.
    JBFundingCycle memory _baseFundingCycle = _getStructFor(
      _projectId,
      _getStructFor(_projectId, _currentConfiguration).basedOn
    );

    // Update the funding cycle to make sure the base's ballot has been approved.
    _updateAndStoreIntrinsicPropertiesOf(
      _configuration,
      _projectId,
      _baseFundingCycle,
      _getLatestTimeAfterBallotOf(_baseFundingCycle, _configuration),
      _weight
    );

    return;
  }

  // Get the active funding cycle's configuration.
  _currentConfiguration = _eligibleOf(_projectId);

  // If an eligible funding cycle does not exist, get a reference to the latest funding cycle configuration for the project. 
  if (_currentConfiguration == 0)
    // Get the latest funding cycle's configuration.
    _currentConfiguration = latestConfigurationOf[_projectId];

  if (!_isConfigurationApproved(_projectId, _currentConfiguration))
    // If it hasn't been approved, set the ID to be the based funding cycle,
    // which carries the last approved configuration.
    _currentConfiguration = _getStructFor(_projectId, _currentConfiguration).basedOn;

  // Get the funding cycle for the configuration.
  JBFundingCycle memory _currentFundingCycle = _getStructFor(_projectId, _currentConfiguration);

  // Make sure the funding cycle is recurring.
  require(_currentFundingCycle.discountRate < 1000000001, '0x1c: NON_RECURRING');

  // Determine if the configurable funding cycle can only take effect on or after a certain date.
  // The ballot must have ended.
  uint256 _mustStartOnOrAfter = _getLatestTimeAfterBallotOf(_currentFundingCycle, _configuration);

  // Initialize a funding cycle.
  _initFor(_projectId, _currentFundingCycle, _configuration, _mustStartOnOrAfter, _weight);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                    | Description                                                                               |
| ------------------------- | ----------------------------------------------------------------------------------------- |
| **`0x1c: NON_RECURRING`** | Thrown if a configurable funding cycle is being looked for but the base is non-recurring. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
