# currentOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**The funding cycle that is currently active for the specified project.**

_Returns an empty funding cycle with all properties set to 0 if a current funding cycle of the project is not found._

## Definition

```solidity
function currentOf(uint256 _projectId)
  public
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get the current funding cycle of.
* The view function can be accessed externally by anyone, and internally by the contract.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).

## Body

1.  If there are no stored funding cycles for the provided project, there can't be an active funding cycle so an empty funding cycle should be returned.

    ```solidity
    // The project must have funding cycles.
    if (latestConfigurationOf[_projectId] == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
    * [`_getStructFor`](\_getstructfor.md)
2.  Get a reference to an eligible funding cycle if there is one. This eligible cycle might not yet be approved.

    ```solidity
    // Get a reference to the configuration of the eligible funding cycle.
    uint256 _fundingCycleConfiguration = _eligibleOf(_projectId);
    ```

    _Internal references:_

    * [`_eligibleOf`](\_eligibleof.md)
3.  Create a reference to a funding cycle.

    ```solidity
    // Keep a reference to the eligible funding cycle.
    JBFundingCycle memory _fundingCycle;
    ```
4.  If there's a candidate funding cycle configuration, check to see if it is approved. If so, return the funding cycle as the `currentOf` the project. Otherwise, get a reference to the funding cycle that the candidate is based on. A current funding cycle will be one derived from this reference.

    ```solidity
    // If a standby funding cycle exists...
    if (_fundingCycleConfiguration > 0) {
      // Resolve the funding cycle for the eligible configuration.
      _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

      // Check to see if this funding cycle's ballot is approved.
      // If so, return it.
      if (_isApproved(_projectId, _fundingCycle)) return _fundingCycle;

      // If it hasn't been approved, set the funding cycle configuration to be the configuration of the funding cycle that it's based on,
      // which carries the last approved configuration.
      _fundingCycleConfiguration = _fundingCycle.basedOn;
    }
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
    * [`_isApproved`](\_isapproved.md)
5.  If there's not a candidate funding cycle configuration, get a reference the latest stored funding cycle for the project. If it's not approved or if it hasn't yet started, get a reference to the cycle it's based on. A current funding cycle will be one derived from this reference.

    ```solidity
    else {
      // No upcoming funding cycle found that is eligible to become active,
      // so use the last configuration.
      _fundingCycleConfiguration = latestConfigurationOf[_projectId];

      // Get the funding cycle for the latest ID.
      _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

      // If it's not approved or if it hasn't yet started, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
      if (!_isApproved(_projectId, _fundingCycle) || block.timestamp < _fundingCycle.start)
        _fundingCycleConfiguration = _fundingCycle.basedOn;
    }
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
    * [`_getStructFor`](\_getstructfor.md)
    * [`_isApproved`](\_isapproved.md)
6.  If the current referenced configuration is 0, there must not be a current cycle so return an empty one.

    ```solidity
    // The funding cycle cant be 0.
    if (_fundingCycleConfiguration == 0) return _getStructFor(0, 0);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
7.  Create the funding cycle structure using the current reference. The current funding cycle will be one based on this reference.

    ```solidity
    // The funding cycle to base a current one on.
    _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
8.  Return a funding cycle based on the one currently referenced. The mock funding cycle is allowed to have started already, which is why a `true` flag is passed in.

    ```solidity
    // Return a mock of the current funding cycle.
    return _mockFundingCycleBasedOn(_fundingCycle, true);
    ```

    _Internal references:_

    * [`_mockFundingCycleBasedOn`](\_mockfundingcyclebasedon.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  The funding cycle that is currently active for the specified project.

  @dev
  Returns an empty funding cycle with all properties set to 0 if a current funding cycle of the project is not found.

  @param _projectId The ID of the project to get the current funding cycle of.

  @return fundingCycle The current funding cycle.
*/
function currentOf(uint256 _projectId)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle)
{
  // The project must have funding cycles.
  if (latestConfigurationOf[_projectId] == 0) return _getStructFor(0, 0);

  // Get a reference to the configuration of the eligible funding cycle.
  uint256 _fundingCycleConfiguration = _eligibleOf(_projectId);

  // Keep a reference to the eligible funding cycle.
  JBFundingCycle memory _fundingCycle;

  // If a standby funding cycle exists...
  if (_fundingCycleConfiguration > 0) {
    // Resolve the funding cycle for the eligible configuration.
    _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

    // Check to see if this funding cycle's ballot is approved.
    // If so, return it.
    if (_isApproved(_projectId, _fundingCycle)) return _fundingCycle;

    // If it hasn't been approved, set the funding cycle configuration to be the configuration of the funding cycle that it's based on,
    // which carries the last approved configuration.
    _fundingCycleConfiguration = _fundingCycle.basedOn;
  } else {
    // No upcoming funding cycle found that is eligible to become active,
    // so use the last configuration.
    _fundingCycleConfiguration = latestConfigurationOf[_projectId];

    // Get the funding cycle for the latest ID.
    _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

    // If it's not approved or if it hasn't yet started, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
    if (!_isApproved(_projectId, _fundingCycle) || block.timestamp < _fundingCycle.start)
      _fundingCycleConfiguration = _fundingCycle.basedOn;
  }

  // The funding cycle cant be 0.
  if (_fundingCycleConfiguration == 0) return _getStructFor(0, 0);

  // The funding cycle to base a current one on.
  _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

  // Return a mock of the current funding cycle.
  return _mockFundingCycleBasedOn(_fundingCycle, true);
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
