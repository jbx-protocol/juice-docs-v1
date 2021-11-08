# \_configurableOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Returns the configurable funding cycle for this project if it exists, otherwise creates one.**

## Definition

```solidity
function _configurableOf(
  uint256 _projectId,
  uint256 _configured,
  uint256 _weight
) private returns (uint256 fundingCycleId) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to find a configurable funding cycle for.
  * `_configured` is the time at which the configuration is occurring.
  * `_weight` is the weight to store along with a newly created configurable funding cycle.
* The function is private to this contract.
* The function returns the ID of a configurable funding cycle.

## Body

1.  If the project does not yet have a funding cycle, initialize a new one that can be configured.

    ```solidity
    // If there's not yet a funding cycle for the project, return the ID of a newly created one.
    if (latestIdOf[_projectId] == 0)
      return _initFor(_projectId, _getStructFor(0), block.timestamp, _weight);
    ```

    _Internal references:_

    * [`latestOf`](../properties/latestidof.md)
    * [`_initFor`](\_initfor.md)
    * [`_getStructFor`](../read/_getstructfor.md)
2.  Get a reference to the project's standby funding cycle.

    ```solidity
    // Get the standby funding cycle's ID.
    fundingCycleId = _standbyOf(_projectId);
    ```

    _Internal references:_

    * [`_standbyOf`](../read/_standbyof.md)
3.  If there is a standby funding cycle, return it after updating it. It must be updated because there's a chance the new reconfiguration will have a new start time than the previous reconfiguration that was in standby, depending on the specifications of the `_baseFundingCycle`'s ballot.

    ```solidity
    // If it exists, make sure its updated, then return it.
    if (fundingCycleId > 0) {
      // Get the funding cycle that the specified one is based on.
      JBFundingCycle memory _baseFundingCycle = _getStructFor(
        _getStructFor(fundingCycleId).basedOn
      );

      // The base's ballot must have ended.
      _updateFundingCycleBasedOn(
        _baseFundingCycle,
        _getLatestTimeAfterBallotOf(_baseFundingCycle, _configured),
        _weight
      );
      return fundingCycleId;
    }
    ```

    _Internal references:_

    * [`_standbyOf`](../read/_standbyof.md)
    * [`_getStructFor`](../read/_getstructfor.md)
    * [`_updateFundingCycleBasedOn`](\_updatefundingcyclebasedon.md)
    * [`_getLatestTimeAfterBallotOf`](../read/_getlatesttimeafterballotof.md)
4.  If there's no standby funding cycle, get a reference to the project's eligible funding cycle. The configurable funding cycle will have to be initialized based on the eligible cycle.

    ```solidity
    // Get the active funding cycle's ID.
    fundingCycleId = _eligibleOf(_projectId);
    ```

    _Internal references:_

    * [`_eligibleOf`](../read/_eligibleof.md)
5.  If there is an eligible cycle, check if it has an approved configuration. If it does not, the configurable funding cycle that will be initialized should not be based on it. Instead, it should be based on the funding cycle that the unapproved funding cycle is based on, which is the last funding cycle with an approved configuration.\
    \
    If an eligible cycle was not found, base the funding cycle that will be initialized on the project's latest funding cycle. If it is not approved, get a reference to the one its based on.

    ```solidity
    // If the ID of an eligible funding cycle exists, it's approved, and active funding cycles are configurable, return it.
    if (fundingCycleId > 0) {
      if (!_isIdApproved(fundingCycleId)) {
        // If it hasn't been approved, set the ID to be the based funding cycle,
        // which carries the last approved configuration.
        fundingCycleId = _getStructFor(fundingCycleId).basedOn;
      } 
    } else {
      // Get the ID of the latest funding cycle which has the latest reconfiguration.
      fundingCycleId = latestIdOf[_projectId];
      
      // If it hasn't been approved, set the ID to be the based funding cycle,
      // which carries the last approved configuration.
      if (!_isIdApproved(fundingCycleId)) fundingCycleId = _getStructFor(fundingCycleId).basedOn;
    }
    ```

    _Internal references:_

    * [`_isIdApproved`](../read/_isidapproved.md)
    * [`_getStructFor`](../read/_getstructfor.md)
    * [`latestIdOf`](../properties/latestidof.md)
6.  At this point, the `fundingCycleId` is the ID of the funding cycle that the one that'll be initialized should be based on. Get a reference to the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md)for the ID.\\

    ```solidity
    // Get a reference to the funding cycle.
    JBFundingCycle memory _fundingCycle = _getStructFor(fundingCycleId);
    ```

    _Internal references:_

    * [`_getStructFor`](../read/_getstructfor.md)
7.  Make sure the cycle is recurring, otherwise throw an error since a new funding cycle cannot be created based on a non-recurring cycle.

    ```solidity
    // Make sure the funding cycle is recurring.
    require(_fundingCycle.discountRate < 10001, '0x1c: NON_RECURRING');
    ```
8.  The next step is to find its appropriate start time constraints for the funding cycle that will be initialized.Get a reference to the timestamp that the initialized funding cycle must start on or after.

    ```solidity
    // Determine if the configurable funding cycle can only take effect on or after a certain date.
    uint256 _mustStartOnOrAfter;
    ```
9.  The funding cycle can start any time after the base funding cycle's ballot's duration is up.\\

    ```solidity
    // The ballot must have ended.
    _mustStartOnOrAfter = _getLatestTimeAfterBallotOf(_fundingCycle, _configured);
    ```

    _Internal references:_

    * [`_getLatestTimeAfterBallotOf`](../read/_getlatesttimeafterballotof.md)
10. Return the ID of the newly initialized funding cycle.\\

    ```solidity
    // Return the newly initialized configurable funding cycle.
    fundingCycleId = _initFor(_projectId, _fundingCycle, _mustStartOnOrAfter, _weight);
    ```

    _Internal references:_

    * [`_initFor`](../read/_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Returns the configurable funding cycle for this project if it exists, otherwise creates one.

  @param _projectId The ID of the project to find a configurable funding cycle for.
  @param _configured The time at which the configuration is occurring.
  @param _weight The weight to store along with a newly created configurable funding cycle.

  @return fundingCycleId The ID of the configurable funding cycle.
*/
function _configurableOf(
  uint256 _projectId,
  uint256 _configured,
  uint256 _weight
) private returns (uint256 fundingCycleId) {
  // If there's not yet a funding cycle for the project, return the ID of a newly created one.
  if (latestIdOf[_projectId] == 0)
    return _initFor(_projectId, _getStructFor(0), block.timestamp, _weight);

  // Get the standby funding cycle's ID.
  fundingCycleId = _standbyOf(_projectId);

  // If it exists, make sure its updated, then return it.
  if (fundingCycleId > 0) {
    // Get the funding cycle that the specified one is based on.
    JBFundingCycle memory _baseFundingCycle = _getStructFor(
      _getStructFor(fundingCycleId).basedOn
    );

    // The base's ballot must have ended.
    _updateFundingCycleBasedOn(
      _baseFundingCycle,
      _getLatestTimeAfterBallotOf(_baseFundingCycle, _configured),
      _weight,
      // No need to copy since a new configuration is going to be applied.
      false
    );
    return fundingCycleId;
  }

  // Get the active funding cycle's ID.
  fundingCycleId = _eligibleOf(_projectId);

  // If the ID of an eligible funding cycle exists, it's approved, and active funding cycles are configurable, return it.
  if (fundingCycleId > 0) {
    if (!_isIdApproved(fundingCycleId)) {
      // If it hasn't been approved, set the ID to be the based funding cycle,
      // which carries the last approved configuration.
      fundingCycleId = _getStructFor(fundingCycleId).basedOn;
    } 
  } else {
    // Get the ID of the latest funding cycle which has the latest reconfiguration.
    fundingCycleId = latestIdOf[_projectId];
    // If it hasn't been approved, set the ID to be the based funding cycle,
    // which carries the last approved configuration.
    if (!_isIdApproved(fundingCycleId)) fundingCycleId = _getStructFor(fundingCycleId).basedOn;
  }

  // Get a reference to the funding cycle.
  JBFundingCycle memory _fundingCycle = _getStructFor(fundingCycleId);

  // Make sure the funding cycle is recurring.
  require(_fundingCycle.discountRate < 201, '0x1c: NON_RECURRING');

  // Determine if the configurable funding cycle can only take effect on or after a certain date.
  uint256 _mustStartOnOrAfter;

  // The ballot must have ended.
  _mustStartOnOrAfter = _getLatestTimeAfterBallotOf(_fundingCycle, _configured);

  // Return the newly initialized configurable funding cycle.
  fundingCycleId = _initFor(_projectId, _fundingCycle, _mustStartOnOrAfter, _weight);
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
