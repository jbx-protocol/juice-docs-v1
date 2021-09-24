# get

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Get the funding cycle with the given ID.**  
  
Definition:

```javascript
function get(uint256 _fundingCycleId)
  external
  view
  override
  returns (FundingCycle memory fundingCycle) { ... }
```

* `_fundingCycleId` is the ID of the funding cycle to get.
* The view function can be accessed externally by anyone. 
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a funding cycle struct.
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Get the funding cycle with the given ID.

  @param _fundingCycleId The ID of the funding cycle to get.

  @return fundingCycle The funding cycle.
*/
function get(uint256 _fundingCycleId)
  external
  view
  override
  returns (FundingCycle memory fundingCycle)
{
  // The funding cycle should exist.
  require(_fundingCycleId > 0, 'NOT_FOUND');

  // See if there's stored info for the provided ID.
  fundingCycle = _getStruct(_fundingCycleId);
  
  // If so, return it.
  if (fundingCycle.number > 0) return fundingCycle;
  
  // Get the current funding cycle. It might exist but not yet have been stored.
  fundingCycle = currentOf(_fundingCycleId);
  
  // If the IDs match, return it.
  if (fundingCycle.id == _fundingCycleId) return fundingCycle;
  
  // Get the queued funding cycle. It might exist but not yet have been stored.
  fundingCycle = queuedOf(_fundingCycleId);
  
  // If the IDs match, return it.
  if (fundingCycle.id == _fundingCycleId) return fundingCycle;

  // Return an empty Funding Cycle.
  return _getStruct(0);
}
```
{% endtab %}

{% tab title="Bug bounty" %}

{% endtab %}
{% endtabs %}

