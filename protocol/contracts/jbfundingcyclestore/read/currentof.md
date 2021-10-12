# currentOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**The funding cycle that is currently active for the specified project.**

_Returns an empty funding cycle with an ID of 0 if a current funding cycle of the project is not found._

_This runs roughly similar logic to _[_`_tappableOf`_](../write/\_tappableof.md)_._\


Definition:

```solidity
function currentOf(uint256 _projectId)
  public
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* `_projectId` is the ID of the project to get the current funding cycle of.
* The view function can be accessed externally by anyone, and internally by the contract.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).



1.  If there are no stored funding cycles for the provided project, there can't be an active funding cycle so an empty funding cycle should be returned.\\

    ```solidity
    // The project must have funding cycles.
    if (latestIdOf[_projectId] == 0) return _getStructFor(0);
    ```

    _Internal references:_

    *   [`_getStructFor`](\_getstructfor.md)


2.  Get a reference to an eligible funding cycle if there is one. This eligible cycle might not yet be approved.

    ```solidity
    // Check for an eligible funding cycle.
    uint256 _fundingCycleId = _eligibleOf(_projectId);
    ```

    _Internal references:_

    *   [`_eligibleOf`](\_eligibleof.md)


3.  If an eligible funding cycle isn't found, check for funding cycle on standby. It's possible that a standby funding cycle will become active once it has been tapped.

    ```solidity
    // If no active funding cycle is found, check if there is a standby funding cycle.
    // If one exists, it will become active one it has been tapped.
    if (_fundingCycleId == 0) _fundingCycleId = _standbyOf(_projectId);
    ```

    _Internal references:_

    *   [`_standbyOf`](\_standbyof.md)


4.  Create a reference to a funding cycle.

    ```solidity
    // Keep a reference to the eligible funding cycle.
    JBFundingCycle memory _fundingCycle;
    ```


5.  If there's a candidate funding cycle ID, check to see if it has started, and if it is approved. If so, return the funding cycle as the `currentOf` the project.\
    \
    Otherwise, get a reference to the funding cycle that the candidate is based on. A current funding cycle will be one derived from this reference.

    ```solidity
    // If a standby funding cycle exists...
    if (_fundingCycleId > 0) {
      // Get the necessary properties for the standby funding cycle.
      _fundingCycle = _getStructFor(_fundingCycleId);

      // Check to see if the correct ballot is approved for this funding cycle, and that it has started.
      if (_fundingCycle.start <= block.timestamp && _isApproved(_fundingCycle))
        return _fundingCycle;

      // If it hasn't been approved, set the ID to be the based funding cycle,
      // which carries the last approved configuration.
      _fundingCycleId = _fundingCycle.basedOn;
    }
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
    *   [`_isApproved`](\_isapproved.md)


6.  If there's not a candidate funding cycle ID, get a reference the latest stored funding cycle for the project. If it's not approved, get a reference to the cycle it's based on. A current funding cycle will be one derived from this reference.

    ```solidity
    else {
      // No upcoming funding cycle found that is eligible to become active,
      // so us the ID of the latest active funding cycle, which carries the last configuration.
      _fundingCycleId = latestIdOf[_projectId];

      // Get the funding cycle for the latest ID.
      _fundingCycle = _getStructFor(_fundingCycleId);

      // If it's not approved, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
      if (!_isApproved(_fundingCycle)) _fundingCycleId = _fundingCycle.basedOn;
    }
    ```

    _Internal references:_

    * [`latestIdOf`](../properties/latestidof.md)
    * [`_getStructFor`](\_getstructfor.md)
    *   [`_isApproved`](\_isapproved.md)


7.  If the current reference ID is 0, there must not be a current cycle so return an empty one with an ID of 0.

    ```solidity
    // The funding cycle cant be 0.
    if (_fundingCycleId == 0) return _getStructFor(0);
    ```

    _Internal references:_

    *   [`_getStructFor`](\_getstructfor.md)


8.  Create the funding cycle structure using the current reference. The current funding cycle will be one based on this reference.

    ```solidity
    // The funding cycle to base a current one on.
    _fundingCycle = _getStructFor(_fundingCycleId);
    ```

    _Internal references:_

    *   [`_getStructFor`](\_getstructfor.md)


9.  Return a funding cycle based on the one currently referenced. This funding cycle will be stored once it has been tapped. The mock funding cycle is allowed to have started already, which is why a `true` flag is passed in.

    ```solidity
    // Return a mock of what the next funding cycle would be like,
    // which would become active one it has been tapped.
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
  Returns an empty funding cycle with an ID of 0 if a current funding cycle of the project is not found.
  
  @dev 
  This runs very similar logic to `_tappableOf`.

  @param _projectId The ID of the project to get the current funding cycle of.

  @return fundingCycle The current funding cycle.
*/
function currentOf(uint256 _projectId)
  public
  view
  override
  returns (JBFundingCycle memory fundingCycle)
{
  // The project must have funding cycles.
  if (latestIdOf[_projectId] == 0) return _getStructFor(0);

  // Check for an eligible funding cycle.
  uint256 _fundingCycleId = _eligibleOf(_projectId);

  // If no active funding cycle is found, check if there is a standby funding cycle.
  // If one exists, it will become active one it has been tapped.
  if (_fundingCycleId == 0) _fundingCycleId = _standbyOf(_projectId);

  // Keep a reference to the eligible funding cycle.
  JBFundingCycle memory _fundingCycle;

  // If a standby funding cycle exists...
  if (_fundingCycleId > 0) {
    // Get the necessary properties for the standby funding cycle.
    _fundingCycle = _getStructFor(_fundingCycleId);

    // Check to see if the correct ballot is approved for this funding cycle, and that it has started.
    if (_fundingCycle.start <= block.timestamp && _isApproved(_fundingCycle))
      return _fundingCycle;

    // If it hasn't been approved, set the ID to be the based funding cycle,
    // which carries the last approved configuration.
    _fundingCycleId = _fundingCycle.basedOn;
  } else {
    // No upcoming funding cycle found that is eligible to become active,
    // so us the ID of the latest active funding cycle, which carries the last configuration.
    _fundingCycleId = latestIdOf[_projectId];

    // Get the funding cycle for the latest ID.
    _fundingCycle = _getStructFor(_fundingCycleId);

    // If it's not approved, get a reference to the funding cycle that the latest is based on, which has the latest approved configuration.
    if (!_isApproved(_fundingCycle)) _fundingCycleId = _fundingCycle.basedOn;
  }

  // The funding cycle cant be 0.
  if (_fundingCycleId == 0) return _getStructFor(0);

  // The funding cycle to base a current one on.
  _fundingCycle = _getStructFor(_fundingCycleId);

  // Return a mock of what the next funding cycle would be like,
  // which would become active once it has been tapped.
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



