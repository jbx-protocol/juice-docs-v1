# queuedOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Get the funding cycle with the given ID.**

_Returns an empty funding cycle with an ID of 0 if a queued funding cycle of the project is not found.  
  
This runs roughly similar logic to_ [_`_configurableOf`_](_configurableof.md)_.  
****_  
Definition:

```javascript
function queuedOf(uint256 _projectId) public view override returns (JBFundingCycle memory) { ... }
```

* `_projectId` is the ID of the project to get the queued funding cycle of.
* The view function can be accessed externally by anyone, and internally by the contract. 
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).

1. If there are no stored funding cycles for the provided project, there can't be a queued funding cycle so an empty funding cycle should be returned.  


   _Internal references:_

   * [`_getStrucFor`](_getstructfor.md)

   ```javascript
   // The project must have funding cycles.
   if (latestIdOf[_projectId] == 0) return _getStructFor(0);
   ```

2. Check to see if there's a standby funding cycle ID.  


   _Internal references:_

   * [`_standbyOf`](_getstructfor.md)

   ```javascript
   // Get a reference to the standby funding cycle.
   uint256 _fundingCycleId = _standbyOf(_projectId);
   ```

3. If there is, it must be the queued funding cycle for the project. Return the funding cycle structure based on this ID.  


   _Internal references:_

   * [`_getStructFor`](_getstructfor.md)

   ```javascript
   // If it exists, return it.
   if (_fundingCycleId > 0) return _getStructFor(_fundingCycleId);
   ```

4. Get the funding cycle thats the `_eligibleOf` the project. A queued funding cycle can be constructed based on the properties of this funding cycle.  


   _Internal references:_

   * [`_eligibleOf`](_getstructfor.md)

   ```javascript
   // Get a reference to the eligible funding cycle.
   _fundingCycleId = _eligibleOf(_projectId);
   ```

5. If there's no eligible cycle, get a reference to the latest cycle stored for the project.  


   _Internal references:_

   * [`latestIdOf`](../properties/latestidof.md)

   ```javascript
   // If an eligible funding cycle doesn't exists...
   if (_fundingCycleId == 0) {
     // No upcoming funding cycle found that is eligible to become active,
     // so use the ID of the latest active funding cycle, which carries the last configured cycle.
     _fundingCycleId = latestIdOf[_projectId];
   }
   ```

6. Get the funding cycle struct for the latest funding cycle.   


   _Internal references:_

   * [`_getStructFor`](_getstructfor.md)

   ```javascript
   // Get the necessary properties for the standby funding cycle.
   JBFundingCycle memory _fundingCycle = _getStructfor(_fundingCycleId);
   ```

7. If it has a duration of 0, there can't be a queued funding cycle since configurations are being made manually instead of on a schedule.   


   _Internal references:_

   * [`_getStructFor`](_getstructfor.md)

   ```javascript
   // There's no queued if the current has a duration of 0.
   if (_fundingCycle.duration == 0) return _getStructFor(0);
   ```

8. Otherwise if it has been approved, return a queued cycle based on it.  


   _Internal references:_

   * [`_isApproved`](_getstructfor.md)
   * [`_mockFundingCycleBasedOn`](_mockfundingcyclebasedon.md)

   ```javascript
   // Check to see if the correct ballot is approved for this funding cycle.
   // If so, return a funding cycle based on it.
   if (_isApproved(_fundingCycle)) return _mockFundingCycleBasedOn(_fundingCycle, false);
   ```

9. Get a reference to the funding cycle that the current eligible cycle is based on which must be the latest approved cycle configuration.

   ```javascript
   // If it hasn't been approved, set the ID to be its base funding cycle, which carries the last approved configuration.
   _fundingCycleId = _fundingCycle.basedOn;
   ```

10. If there's not a reference to a possible funding cycle to base a queued cycle on, there must not be a queued cycle.  


    _Internal references:_

    * [`_getStructFor`](_getstructfor.md)

    ```javascript
    // A funding cycle must exist.
    if (_fundingCycleId == 0) return _getStructFor(0);
    ```

11. Return a funding cycle based on the one current referenced, which must be the last approved cycle.

    ```javascript
    // Return a mock of what its second next up funding cycle would be.
    // Use second next because the next would be a mock of the current funding cycle.
    return _mockFundingCycleBasedOn(_getStructFor(_fundingCycleId), false);
    ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  The funding cycle that's next up for a project, and therefor not currently accepting payments.

  @dev 
  This runs roughly similar logic to `_configurable`.
  
  @dev
  Returns an empty funding cycle with an ID of 0 if a queued funding cycle of the project is not found.

  @param _projectId The ID of the project to get the queued funding cycle of.

  @return _fundingCycle The queued funding cycle.
*/
function queuedOf(uint256 _projectId) public view override returns (JBFundingCycle memory) {
  // The project must have funding cycles.
  if (latestIdOf[_projectId] == 0) return _getStructFor(0);

  // Get a reference to the standby funding cycle.
  uint256 _fundingCycleId = _standbyOf(_projectId);

  // If it exists, return it.
  if (_fundingCycleId > 0) return _getStructFor(_fundingCycleId);

  // Get a reference to the eligible funding cycle.
  _fundingCycleId = _eligibleOf(_projectId);

  // If an eligible funding cycle doesn't exists...
  if (_fundingCycleId == 0) {
    // No upcoming funding cycle found that is eligible to become active,
    // so use the ID of the latest active funding cycle, which carries the last configured cycle.
    _fundingCycleId = latestIdOf[_projectId];
  }
    
  // Get the necessary properties for the standby funding cycle.
  JBFundingCycle memory _fundingCycle = _getStructFor(_fundingCycleId);

  // There's no queued if the current has a duration of 0.
  if (_fundingCycle.duration == 0) return _getStructFor(0);

  // Check to see if the correct ballot is approved for this funding cycle.
  // If so, return a funding cycle based on it.
  if (_isApproved(_fundingCycle)) return _mockFundingCycleBasedOn(_fundingCycle, false);

  // If it hasn't been approved, set the ID to be its base funding cycle, which carries the last approved configuration.
  _fundingCycleId = _fundingCycle.basedOn;

  // A funding cycle must exist.
  if (_fundingCycleId == 0) return _getStructFor(0);

  // Return a mock of what its second next up funding cycle would be.
  // Use second next because the next would be a mock of the current funding cycle.
  return _mockFundingCycleBasedOn(_getStructFor(_fundingCycleId), false);
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}

