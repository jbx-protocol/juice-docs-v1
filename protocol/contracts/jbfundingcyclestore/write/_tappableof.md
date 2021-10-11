# \_tappableOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Returns the funding cycle that can be tapped at the time of the call.**\
****\
****Definition:

```solidity
function _tappableOf(uint256 _projectId) private returns (uint256 fundingCycleId) { ... }
```

* `_projectId` is the ID of the project to find a tappable funding cycle for.
* The function is private to this contract.
* The function returns the ID of a tappable funding cycle.



1.  Get a reference to the project's eligible funding cycle.\


    _Internal references:_

    * [`_eligibleOf`](../read/\_eligibleof.md)

    ```javascript
    // Check for the ID of an eligible funding cycle.
    fundingCycleId = _eligibleOf(_projectId);
    ```


2.  If no eligible funding cycle was found, get a reference to the project's standby cycle.\


    _Internal references:_

    * [`_standbyOf`](../read/\_standbyof.md)

    ```javascript
    // No eligible funding cycle found, check for the ID of a standby funding cycle.
    // If this one exists, it will become eligible one it has started.
    if (fundingCycleId == 0) fundingCycleId = _standbyOf(_projectId);
    ```


3.  Get a reference to the funding cycle that should be tapped.

    ```javascript
    // Keep a reference to the funding cycle eligible for being tappable.
    JBFundingCycle memory _fundingCycle;
    ```


4.  If a funding cycle exists that is either eligible to be tapped or is in standby, check to see if it has started and it's approved. If so, return it. Otherwise, get a reference to the funding cycle that it's based on which will provide the correct configuration to a new funding cycle that will be initialized.\
    \
    If no eligible or standby funding cycle exists, get a reference to the last approved funding cycle for the project which will provide the correct configuration to a new funding cycle that will be initialized.\


    _Internal references:_

    * [`_getStructFor`](../read/\_getstructfor.md)
    * [`_isApproved`](../read/\_isapproved.md)
    * [`latestIdOf`](../properties/latestidof.md)

    ```javascript
    // If the ID of an eligible funding cycle exists,
    // check to see if it has been approved by the based funding cycle's ballot.
    if (fundingCycleId > 0) {
      // Get the necessary properties for the funding cycle.
      _fundingCycle = _getStructFor(fundingCycleId);

      // Check to see if the cycle is approved. If so, return it.
      if (_fundingCycle.start <= block.timestamp && _isApproved(_fundingCycle))
        return fundingCycleId;

      // If it hasn't been approved, set the ID to be the base funding cycle,
      // which carries the last approved configuration.
      fundingCycleId = _fundingCycle.basedOn;
    } else {
      // No upcoming funding cycle found that is eligible to become active, clone the latest active funding cycle.
      // which carries the last configuration.
      fundingCycleId = latestIdOf[_projectId];

      // Get the funding cycle for the latest ID.
      _fundingCycle = _getStructFor(fundingCycleId);

      // If it's not approved, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
      if (!_isApproved(_fundingCycle)) fundingCycleId = _fundingCycle.basedOn;
    }
    ```


5.  Make sure there's a funding cycle to base the newly initialized cycle on.

    ```javascript
    // The funding cycle cant be 0.
    require(fundingCycleId > 0, '0x1d: NOT_FOUND');
    ```


6.  Get a reference to the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) structure.\


    _Internal references:_

    * [`_getStructFor`](../read/\_getstructfor.md)

    ```javascript
    // Set the eligible funding cycle.
    _fundingCycle = _getStructFor(fundingCycleId);
    ```


7.  Make sure the cycle is recurring, otherwise throw an error since a new funding cycle cannot be created based on a non-recurring cycle.

    ```javascript
    // Funding cycles with a discount rate of 100% are non-recurring.
    require(_fundingCycle.discountRate < 10001, '0x1e: NON_RECURRING');
    ```


8.  Get a reference to the first start time that is possible after the funding cycle, which is the moment after it ends.\


    _Internal references:_

    * [`_SECONDS_IN_DAY`](../properties/\_seconds_in_day.md)

    ```javascript
    // The time when the funding cycle immediately after the eligible funding cycle starts.
    uint256 _nextImmediateStart = _fundingCycle.start + (_fundingCycle.duration * _SECONDS_IN_DAY);
    ```


9.  Get a reference to the time distance to the most recent funding cycle iteration's start time.\


    _Internal references:_

    * [`_SECONDS_IN_DAY`](../properties/\_seconds_in_day.md)

    ```javascript
    // The distance from now until the nearest past multiple of the cycle duration from its start.
    // A duration of zero means the reconfiguration can start right away.
    uint256 _timeFromImmediateStartMultiple = _fundingCycle.duration == 0
      ? 0
      : (block.timestamp - _nextImmediateStart) % (_fundingCycle.duration * _SECONDS_IN_DAY);
    ```


10. Set the ID that'll be returned to the ID of a newly initialized funding cycle.\


    _Internal references:_

    * [`_initFor`](\_initfor.md)

    ```javascript
    // Return the tappable funding cycle.
    fundingCycleId = _initFor(
      _projectId,
      _fundingCycle,
      block.timestamp - _timeFromImmediateStartMultiple,
      0
    );
    ```


11. Copy the properties of the base funding cycle onto the newly initialized configuration.\


    _Internal references:_

    * [`_packAndStoreConfigurationPropertiesOf`](\_packandstoreconfigurationpropertiesof.md)
    * [`_metadataOf`](../properties/\_metadataof.md)
    * [`_targetOf`](../properties/\_targetof.md)

    ```javascript
     // Copy the properties of the base funding cycle onto the new configuration efficiently.
    _packAndStoreConfigurationPropertiesOf(
      fundingCycleId,
      _fundingCycle.configured,
      _fundingCycle.ballot,
      _fundingCycle.duration,
      _fundingCycle.currency,
      _fundingCycle.fee,
      _fundingCycle.discountRate
     );

     _metadataOf[fundingCycleId] = _metadataOf[_fundingCycle.id];
     _targetOf[fundingCycleId] = _targetOf[_fundingCycle.id];
    ```
{% endtab %}

{% tab title="Only code" %}
```solidity
/**
  @notice 
  Returns the funding cycle that can be tapped at the time of the call.

  @param _projectId The ID of the project to find a tappable funding cycle for.

  @return fundingCycleId The ID of the tappable funding cycle.
*/
function _tappableOf(uint256 _projectId) private returns (uint256 fundingCycleId) {
  // Check for the ID of an eligible funding cycle.
  fundingCycleId = _eligibleOf(_projectId);

  // No eligible funding cycle found, check for the ID of a standby funding cycle.
  // If this one exists, it will become eligible one it has started.
  if (fundingCycleId == 0) fundingCycleId = _standbyOf(_projectId);

  // Keep a reference to the funding cycle eligible for being tappable.
  JBFundingCycle memory _fundingCycle;

  // If the ID of an eligible funding cycle exists,
  // check to see if it has been approved by the based funding cycle's ballot.
  if (fundingCycleId > 0) {
    // Get the necessary properties for the funding cycle.
    _fundingCycle = _getStructFor(fundingCycleId);

    // Check to see if the cycle is approved. If so, return it.
    if (_fundingCycle.start <= block.timestamp && _isApproved(_fundingCycle))
      return fundingCycleId;

    // If it hasn't been approved, set the ID to be the base funding cycle,
    // which carries the last approved configuration.
    fundingCycleId = _fundingCycle.basedOn;
  } else {
    // No upcoming funding cycle found that is eligible to become active, clone the latest active funding cycle.
    // which carries the last configuration.
    fundingCycleId = latestIdOf[_projectId];

    // Get the funding cycle for the latest ID.
    _fundingCycle = _getStructFor(fundingCycleId);

    // If it's not approved, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
    if (!_isApproved(_fundingCycle)) fundingCycleId = _fundingCycle.basedOn;
  }

  // The funding cycle cant be 0.
  require(fundingCycleId > 0, '0x1d: NOT_FOUND');

  // Set the eligible funding cycle.
  _fundingCycle = _getStructFor(fundingCycleId);

  // Funding cycles with a discount rate of 100% are non-recurring.
  require(_fundingCycle.discountRate < 201, '0x1e: NON_RECURRING');

  // The time when the funding cycle immediately after the eligible funding cycle starts.
  uint256 _nextImmediateStart = _fundingCycle.start + (_fundingCycle.duration * _SECONDS_IN_DAY);

  // The distance from now until the nearest past multiple of the cycle duration from its start.
  // A duration of zero means the reconfiguration can start right away.
  uint256 _timeFromImmediateStartMultiple = _fundingCycle.duration == 0
    ? 0
    : (block.timestamp - _nextImmediateStart) % (_fundingCycle.duration * _SECONDS_IN_DAY);

  // Return the tappable funding cycle.
  fundingCycleId = _initFor(
    _projectId,
    _fundingCycle,
    block.timestamp - _timeFromImmediateStartMultiple,
    0
  );
  
  // Copy the properties of the base funding cycle onto the new configuration efficiently.
  _packAndStoreConfigurationPropertiesOf(
    fundingCycleId,
    _fundingCycle.configured,
    _fundingCycle.ballot,
    _fundingCycle.duration,
    _fundingCycle.currency,
    _fundingCycle.fee,
    _fundingCycle.discountRate
  );

  _metadataOf[fundingCycleId] = _metadataOf[_fundingCycle.id];
  _targetOf[fundingCycleId] = _targetOf[_fundingCycle.id];
}
```
{% endtab %}

{% tab title="Errors" %}
| String                    | Description                                                                           |
| ------------------------- | ------------------------------------------------------------------------------------- |
| **`0x1d: NOT_FOUND`**     | Thrown if no tappable funding cycle was found.                                        |
| **`0x1e: NON_RECURRING`** | Thrown if a tappable funding cycle is being looked for but the base is non-recurring. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

