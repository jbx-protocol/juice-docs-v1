# queuedOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**The funding cycle that's next up for the specified project.**

_Returns an empty funding cycle with all properties set to 0 if a queued funding cycle of the project is not found._

## Definition

```solidity
function queuedOf(uint256 _projectId) public view override returns (JBFundingCycle memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get the queued funding cycle of.
* The view function can be accessed externally by anyone, and internally by the contract.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).

## Body

1.  If there are no stored funding cycles for the provided project, there can't be a queued funding cycle so an empty funding cycle should be returned.

    ```solidity
    // The project must have funding cycles.
    if (latestConfigurationOf[_projectId] == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
2.  Check to see if there's a standby funding cycle configuration.

    ```solidity
    // Get a reference to the configuration of the standby funding cycle.
    uint256 _fundingCycleConfiguration = _standbyOf(_projectId);
    ```

    _Internal references:_

    * [`_standbyOf`](\_getstructfor.md)
3.  If there is, it must be the queued funding cycle for the project. Return the funding cycle structure based on this configuration.

    ```solidity
    // If it exists, return it's funding cycle.
    if (_fundingCycleConfiguration > 0)
      return _getStructFor(_projectId, _fundingCycleConfiguration);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
4.  Get the last stored funding cycle for the project. A queued funding cycle can be constructed based on the properties of this funding cycle.

    ```solidity
    // Get a reference to the latest stored funding cycle configuration for the project.
    _fundingCycleConfiguration = latestConfigurationOf[_projectId];
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
5.  Get the funding cycle struct for the latest funding cycle.

    ```solidity
    // Resolve the funding cycle for the for the latest configured funding cycle.
    JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
6.  If it has a duration of 0, there can't be a queued funding cycle since configurations are being made manually instead of on a schedule.

    ```solidity
    // There's no queued if the current has a duration of 0.
    if (_fundingCycle.duration == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
7.  If it has a discount rate of of 1000000001, there can't be a queued funding cycle since there was only a one-time configuration.

    ```solidity
     // There's no queued if the current is non recurring, represented by a discount rate of 1000000001.
    if (_fundingCycle.discountRate == 1000000001) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
8.  Otherwise if it has been approved, return a queued cycle based on it. The mock funding cycle is not allowed to have started already, which is why a `false` flag is passed in.

    ```solidity
    // Check to see if this funding cycle's ballot is approved.
    // If so, return a funding cycle based on it.
    if (_isApproved(_projectId, _fundingCycle))
      return _mockFundingCycleBasedOn(_fundingCycle, false);
    ```

    _Internal references:_

    * [`_isApproved`](\_getstructfor.md)
    * [`_mockFundingCycleBasedOn`](\_mockfundingcyclebasedon.md)
9.  Get a reference to the funding cycle that the current eligible cycle is based on which must be the latest approved cycle configuration.

    ```solidity
    // If it hasn't been approved, set the configuration to be that of its base funding cycle, which carries the last approved configuration.
    _fundingCycleConfiguration = _fundingCycle.basedOn;
    ```
10. If there's not a reference to a possible funding cycle to base a queued cycle on, there must not be a queued cycle.

    ```solidity
    // A funding cycle must exist.
    if (_fundingCycleConfiguration == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
11. Return a funding cycle based on the one current referenced, which must be the last approved cycle. The mock funding cycle is not allowed to have started already, which is why a `false` flag is passed in.

````
```solidity
````

````
// Return a mock of the next up funding cycle.
// Use second next because the next would be a mock of the current funding cycle, not the queued one.
return _mockFundingCycleBasedOn(_getStructFor(_projectId, _fundingCycleConfiguration), false);
```

_Internal references:_

* [`_getStructFor`](\_getstructfor.md)
* [`_mockFundingCycleBasedOn`](\_mockfundingcyclebasedon.md)
````
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  The funding cycle that's next up for the specified project.

  @dev
  Returns an empty funding cycle with all properties set to 0 if a queued funding cycle of the project is not found.

  @param _projectId The ID of the project to get the queued funding cycle of.

  @return _fundingCycle The queued funding cycle.
*/
function queuedOf(uint256 _projectId) public view override returns (JBFundingCycle memory) {
  // The project must have funding cycles.
  if (latestConfigurationOf[_projectId] == 0) return _getStructFor(0, 0);

  // Get a reference to the configuration of the standby funding cycle.
  uint256 _fundingCycleConfiguration = _standbyOf(_projectId);

  // If it exists, return it's funding cycle.
  if (_fundingCycleConfiguration > 0)
    return _getStructFor(_projectId, _fundingCycleConfiguration);

  // Get a reference to the latest stored funding cycle configuration for the project.
  _fundingCycleConfiguration = latestConfigurationOf[_projectId];

  // Resolve the funding cycle for the for the latest configured funding cycle.
  JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

  // There's no queued if the current has a duration of 0.
  if (_fundingCycle.duration == 0) return _getStructFor(0, 0);

  // There's no queued if the current is non recurring, represented by a discount rate of 1000000001.
  if (_fundingCycle.discountRate == 1000000001) return _getStructFor(0, 0);

  // Check to see if this funding cycle's ballot is approved.
  // If so, return a funding cycle based on it.
  if (_isApproved(_projectId, _fundingCycle))
    return _mockFundingCycleBasedOn(_fundingCycle, false);

  // If it hasn't been approved, set the configuration to be that of its base funding cycle, which carries the last approved configuration.
  _fundingCycleConfiguration = _fundingCycle.basedOn;

  // A funding cycle must exist.
  if (_fundingCycleConfiguration == 0) return _getStructFor(0, 0);

  // Return a mock of the next up funding cycle.
  // Use second next because the next would be a mock of the current funding cycle, not the queued one.
  return _mockFundingCycleBasedOn(_getStructFor(_projectId, _fundingCycleConfiguration), false);
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
