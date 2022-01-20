# \_eligibleOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The project's stored funding cycle that has started and hasn't yet expired.**

_A value of 0 is returned if no funding cycle was found._

_Assumes the project has a latest configuration._

## Definition

```solidity
function _eligibleOf(uint256 _projectId) private view returns (uint256 configuration) { ... } 
```

* Arguments:
  * `_projectId` is the ID of a project to look through for an eligible cycle.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the configuration of an eligible funding cycle if one exists, or 0 if one doesn't exist.

## Body

1.  Get a reference to the latest funding cycle for the project.

    ```solidity
    // Get a reference to the project's latest funding cycle.
    configuration = latestConfigurationOf[_projectId];
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
2.  Get the struct for the latest funding cycle.

    ```solidity
    // Get the latest funding cycle.
    JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, configuration);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
3.  If the latest is expired, return an empty funding cycle since there can't be a stored eligible cycle.

    ```solidity
    // If the latest is expired, return an empty funding cycle.
    // A duration of 0 can not be expired.
    if (
      _fundingCycle.duration > 0 && block.timestamp >= _fundingCycle.start + _fundingCycle.duration
    ) return 0;
    ```
4.  If the funding cycle has started, it must be eligible.

    ```solidity
    // Return the funding cycle's configuration if it has started.
    if (block.timestamp >= _fundingCycle.start) return _fundingCycle.configuration;
    ```
5.  Get a reference to the funding cycle that the current cycle is based on.

    ```solidity
    // Get a reference to the cycle's base configuration.
    JBFundingCycle memory _baseFundingCycle = _getStructFor(_projectId, _fundingCycle.basedOn);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
6.  If the base is expired, return an empty funding cycle since there can't be a stored eligible cycle.

    ```solidity
    // If the base cycle isn't eligible, the project has no eligible cycle.
    // A duration of 0 is always eligible.
    if (
      _baseFundingCycle.duration > 0 &&
      block.timestamp >= _baseFundingCycle.start + _baseFundingCycle.duration
    ) return 0;
    ```
7.  Return the configuration that the latest funding cycle is based on.

    ```solidity
    // Return the configuration that the latest funding cycle is based on.
    configuration = _fundingCycle.basedOn;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  The project's stored funding cycle that has started and hasn't yet expired.
  
  @dev
  A value of 0 is returned if no funding cycle was found.

  @dev
  Assumes the project has a latest configuration.

  @param _projectId The ID of the project to look through.

  @return configuration The configuration of the active funding cycle.
*/
function _eligibleOf(uint256 _projectId) private view returns (uint256 configuration) {
  // Get a reference to the project's latest funding cycle.
  configuration = latestConfigurationOf[_projectId];

  // Get the latest funding cycle.
  JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, configuration);

  // If the latest is expired, return an empty funding cycle.
  // A duration of 0 can not be expired.
  if (
    _fundingCycle.duration > 0 && block.timestamp >= _fundingCycle.start + _fundingCycle.duration
  ) return 0;

  // Return the funding cycle's configuration if it has started.
  if (block.timestamp >= _fundingCycle.start) return _fundingCycle.configuration;

  // Get a reference to the cycle's base configuration.
  JBFundingCycle memory _baseFundingCycle = _getStructFor(_projectId, _fundingCycle.basedOn);

  // If the base cycle isn't eligible, the project has no eligible cycle.
  // A duration of 0 is always eligible.
  if (
    _baseFundingCycle.duration > 0 &&
    block.timestamp >= _baseFundingCycle.start + _baseFundingCycle.duration
  ) return 0;

  // Return the configuration that the latest funding cycle is based on.
  configuration = _fundingCycle.basedOn;
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
