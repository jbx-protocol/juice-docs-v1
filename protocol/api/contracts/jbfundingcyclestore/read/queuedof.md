# queuedOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**The funding cycle that's next up for the specified project.**

_If a queued funding cycle of the project is not found, returns an empty funding cycle with all properties set to 0._

### Definition

```solidity
function queuedOf(uint256 _projectId)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get the queued funding cycle of.
* The view function can be accessed externally by anyone, and internally by the contract.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBFundingCycleStore`](../../../interfaces/) interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).

### Body

1.  If there are no stored funding cycles for the provided project, there can't be a queued funding cycle so an empty funding cycle should be returned.

    ```solidity
    // If the project does not have a funding cycle, return an empty struct.
    if (latestConfigurationOf[_projectId] == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](_getstructfor.md)
2.  Check to see if there's a standby funding cycle configuration.

    ```solidity
    // Get a reference to the configuration of the standby funding cycle.
    uint256 _standbyFundingCycleConfiguration = _standbyOf(_projectId);
    ```

    _Internal references:_

    * [`_standbyOf`](_getstructfor.md)
3.  If there is a stanby cycle and it is approved, it must be the queued funding cycle for the project. Otherwise get a reference to the funding cycle structure based on the yet-to-be-approved standby configuration.

    ```solidity
    // If it exists, return its funding cycle if it is approved.
    if (_standbyFundingCycleConfiguration > 0) {
      fundingCycle = _getStructFor(_projectId, _standbyFundingCycleConfiguration);

      if (_isApproved(_projectId, fundingCycle)) return fundingCycle;

      // Resolve the funding cycle for the latest configured funding cycle.
      fundingCycle = _getStructFor(_projectId, fundingCycle.basedOn);
    }
    ```

    _Internal references:_

    * [`_isApproved`](_isapproved.md)
    * [`_getStructFor`](_getstructfor.md)
4.  If there is no standby funding cycle, get the last stored funding cycle for the project. If it has already started, a queued funding cycle can be constructed based on the properties of this funding cycle.

    ```solidity
    else {
      // Resolve the funding cycle for the latest configured funding cycle.
      fundingCycle = _getStructFor(_projectId, latestConfigurationOf[_projectId]);
      
      // If the latest funding cycle starts in the future, it must start in the distant future
      // since its not in standby. In this case base the queued cycles on the base cycle.
      if (fundingCycle.start > block.timestamp)
        fundingCycle = _getStructFor(_projectId, fundingCycle.basedOn);
    }
    ```

    _Internal references:_

    * [`_getStructFor`](_getstructfor.md)
    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
5.  If the referenced funding cycle has a duration of 0, there can't be a queued funding cycle since configurations with no duration are being made manually instead of on a schedule.

    ```solidity
    // There's no queued if the current has a duration of 0.
    if (fundingCycle.duration == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](_getstructfor.md)
6.  If the referenced funding cycle has been approved, return a queued cycle based on it. The mock funding cycle is not allowed to have started already, which is why a `false` flag is passed in.

    ```solidity
    // Check to see if this funding cycle's ballot is approved.
    // If so, return a funding cycle based on it.
    if (_isApproved(_projectId, fundingCycle)) return _mockFundingCycleBasedOn(fundingCycle, false);
    ```

    _Internal references:_

    * [`_isApproved`](_getstructfor.md)
    * [`_mockFundingCycleBasedOn`](_mockfundingcyclebasedon.md)
7.  Get a reference to the funding cycle that the current eligible cycle is based on which must be the latest approved cycle configuration.

    ```solidity
    // Get the funding cycle of its base funding cycle, which carries the last approved configuration.
    fundingCycle = _getStructFor(_projectId, fundingCycle.basedOn);
    ```
8.  Return a funding cycle based on the one current referenced, which must be the last approved cycle. The mock funding cycle is not allowed to have started already, which is why a `false` flag is passed in.

    ```solidity
    // Return a mock of the next up funding cycle.
    return _mockFundingCycleBasedOn(fundingCycle, false);
    ```

    _Internal references:_

    * [`_mockFundingCycleBasedOn`](_mockfundingcyclebasedon.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  The funding cycle that's next up for the specified project.

  @dev
  If a queued funding cycle of the project is not found, returns an empty funding cycle with all properties set to 0.

  @param _projectId The ID of the project to get the queued funding cycle of.

  @return fundingCycle The project's queued funding cycle.
*/
function queuedOf(uint256 _projectId)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle)
{
  // If the project does not have a funding cycle, return an empty struct.
  if (latestConfigurationOf[_projectId] == 0) return _getStructFor(0, 0);

  // Get a reference to the configuration of the standby funding cycle.
  uint256 _standbyFundingCycleConfiguration = _standbyOf(_projectId);

  // If it exists, return its funding cycle if it is approved.
  if (_standbyFundingCycleConfiguration > 0) {
    fundingCycle = _getStructFor(_projectId, _standbyFundingCycleConfiguration);

    if (_isApproved(_projectId, fundingCycle)) return fundingCycle;

    // Resolve the funding cycle for the latest configured funding cycle.
    fundingCycle = _getStructFor(_projectId, fundingCycle.basedOn);
  } else {
    // Resolve the funding cycle for the latest configured funding cycle.
    fundingCycle = _getStructFor(_projectId, latestConfigurationOf[_projectId]);

    // If the latest funding cycle starts in the future, it must start in the distant future
    // since its not in standby. In this case base the queued cycles on the base cycle.
    if (fundingCycle.start > block.timestamp)
      fundingCycle = _getStructFor(_projectId, fundingCycle.basedOn);
  }

  // There's no queued if the current has a duration of 0.
  if (fundingCycle.duration == 0) return _getStructFor(0, 0);

  // Check to see if this funding cycle's ballot is approved.
  // If so, return a funding cycle based on it.
  if (_isApproved(_projectId, fundingCycle)) return _mockFundingCycleBasedOn(fundingCycle, false);

  // Get the funding cycle of its base funding cycle, which carries the last approved configuration.
  fundingCycle = _getStructFor(_projectId, fundingCycle.basedOn);

  // Return a mock of the next up funding cycle.
  return _mockFundingCycleBasedOn(fundingCycle, false);
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
