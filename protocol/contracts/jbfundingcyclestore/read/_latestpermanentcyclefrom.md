# \_latestPermanentCycleFrom

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Finds the last funding cycle that was permanent in relation to the specified funding cycle.**  
  
_A permanent funding cycle is one with a \`cycleLimit\` of 0 is, or one that isn't based on any previous funding cycle._

_If the provided cycle is permanent, it will be returned._

# Definition

```solidity
function _latestPermanentCycleFrom(JBFundingCycle memory _fundingCycle)
  private
  view
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_fundingCycle` is the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to get the latest permanent cycle from.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the latest permanent funding cycle. If the provided cycle is permanent, it will be returned.

# Body 

1. If the provided `_fundingCycle` has a `cycleLimit` of 0, or if it isn't `basedOn` another funding cycle, return it.

   ```solidity
   // A funding cycle with no cycle limit, or one not based on another funding cycle is, implicitly permanent.
   if (_fundingCycle.basedOn == 0 || _fundingCycle.cycleLimit == 0) return _fundingCycle;
   ```

2. Get a reference to the base funding cycle.

   ```solidity
   // Get the funding cycle of the base funding cycle.
   fundingCycle = _getStructFor(_fundingCycle.basedOn);  
   ```

3. Recurse through this function with the base funding cycle until a permanent one is found.

   ```solidity
   // Recursively check if the previous cycle is permanent.
   return _latestPermanentCycleFrom(fundingCycle);
   ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Finds the last funding cycle that was permanent in relation to the specified funding cycle.

  @dev
  A permanent funding cycle is one with a `cycleLimit` of 0 is, or one that isn't based on any previous funding cycle.

  @dev
  If the provided cycle is permanent, it will be returned.
  
  @param _fundingCycle The funding cycle to find the most recent permanent cycle compared to.

  @return fundingCycle The most recent permanent funding cycle.
*/
function _latestPermanentCycleFrom(JBFundingCycle memory _fundingCycle)
  private
  view
  returns (JBFundingCycle memory fundingCycle)
{
  // A funding cycle with no cycle limit, or one not based on another funding cycle is, implicitly permanent.
  if (_fundingCycle.basedOn == 0 || _fundingCycle.cycleLimit == 0) return _fundingCycle;
  
  // Get the funding cycle of the base funding cycle.
  fundingCycle = _getStructFor(_fundingCycle.basedOn);
  
  // Recursively check if the previous cycle is permanent.
  return _latestPermanentCycleFrom(fundingCycle);
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

