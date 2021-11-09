# \_initFor

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Initializes a funding cycle with the appropriate properties.**

# Definition

```solidity
function _initFor(
  uint256 _projectId,
  JBFundingCycle memory _baseFundingCycle,
  uint256 _configuration,
  uint256 _mustStartOnOrAfter,
  uint256 _weight
) private { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funding cycle being initialized belongs.
  * `_baseFundingCycle` is the funding cycle to base the initialized one on.
  * `_configuration` is the configuration of the funding cycle being initialized.
  * `_mustStartOnOrAfter` is the time before which the initialized funding cycle can't start.
  * `_weight` is the weight to give the newly initialized funding cycle.
* The function is private to this contract.
* The function doesn't return anything.

# Body

1.  If no base funding cycle was provided, create a first funding cycle for the project. Otherwise, create a new funding cycle by calling`_updateFundingCycleBasedOn`, which will derive properties for the funding cycle that follows the specified base cycle, and store them to an ID.

    ```solidity
    // If there is no base, initialize a first cycle.
    if (_baseFundingCycle.number == 0) {
      // The first number is 1.
      uint256 _number = 1;

      // Get the formatted ID.
      newFundingCycleId = _idFor(_projectId, _number);

      // Set fresh intrinsic properties.
      _packAndStoreIntrinsicPropertiesOf(
        _configuration,
        _projectId,
        _number,
        _weight,
        _baseFundingCycle.configuration,
        block.timestamp
      );
    } else {
      // Update the intrinsic properties of the funding cycle being initialized.
      _updateAndStoreIntrinsicPropertiesOf(
        _configuration,
        _projectId,
        _baseFundingCycle,
        _mustStartOnOrAfter,
        _weight
      );
    }
    ```

    _Internal references:_

    * [`_packAndStoreIntrinsicPropertiesOf`](\_packandstoreintrinsicpropertiesof.md)
    * [`_updateAndStoreIntrinsicPropertiesOf`](\_updateandstoreintrinsicpropertiesOf.md)
2.  Store the initialized ID as the `latestIdOf` the project.

    ```solidity
    // Set the project's latest funding cycle configuration.
    latestConfigurationOf[_projectId] = _configuration;
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
3.  Emit an `Init` event with the relevant parameters.

    ```solidity
    emit Init(_configuration, _projectId, _basedFundingCycle.configuration);
    ```

    _Event references:_

    * [`Init`](../events/init.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Initializes a funding cycle with the appropriate properties.

  @param _projectId The ID of the project to which the funding cycle being initialized belongs.
  @param _baseFundingCycle The funding cycle to base the initialized one on.
  @param _configuration The configuration of the funding cycle being initialized.
  @param _mustStartOnOrAfter The time before which the initialized funding cycle can't start.
  @param _weight The weight to give the newly initialized funding cycle.
*/
function _initFor(
  uint256 _projectId,
  JBFundingCycle memory _baseFundingCycle,
  uint256 _configuration,
  uint256 _mustStartOnOrAfter,
  uint256 _weight
) private {
  // If there is no base, initialize a first cycle.
  if (_baseFundingCycle.number == 0) {
    // The first number is 1.
    uint256 _number = 1;

    // Set fresh intrinsic properties.
    _packAndStoreIntrinsicPropertiesOf(
      _configuration,
      _projectId,
      _number,
      _weight,
      _baseFundingCycle.configuration,
      block.timestamp
    );
  } else {
    // Update the intrinsic properties of the funding cycle being initialized.
    _updateAndStoreIntrinsicPropertiesOf(
      _configuration,
      _projectId,
      _baseFundingCycle,
      _mustStartOnOrAfter,
      _weight
    );
  }

  // Set the project's latest funding cycle configuration.
  latestConfigurationOf[_projectId] = _configuration;

  emit Init(_configuration, _projectId, _baseFundingCycle.configuration);
}
```
{% endtab %}

{% tab title="Events" %}
| Name       | Data                                                                                                                                                                                                 |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`Init`** | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed basedOn</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
