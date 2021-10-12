# get

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Get the funding cycle with the given ID.**

Definition:

```solidity
function get(uint256 _fundingCycleId)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* `_fundingCycleId` is the ID of the funding cycle to get.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).



1.  Check that the provided funding cycle ID is valid.

    ```solidity
    // The funding cycle should exist.
    require(_fundingCycleId > 0, '0x13 BAD_ID');
    ```


2.  Try to get the full funding cycle struct for the provided ID.

    ```solidity
    // See if there's stored info for the provided ID.
    fundingCycle = _getStructFor(_fundingCycleId);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)


3.  If the funding cycle exists in storage, return it.

    ```solidity
    // If so, return it.
    if (fundingCycle.number > 0) return fundingCycle;
    ```


4.  If the funding cycle was not found in storage, it's possible that it still exists as a consequence of a previous funding cycle rolling over, but hasn't yet been stored. Check to see if there's a current funding cycle.

    ```solidity
    // Get the current funding cycle. It might exist but not yet have been stored.
    fundingCycle = currentOf(_fundingCycleId);
    ```

    _Internal references:_

    * [`currentOf`](currentof.md)


5.  If the funding cycle ID being queried matches the current funding cycle of the project, return it.

    ```solidity
     // If the IDs match, return it.
     if (fundingCycle.id == _fundingCycleId) return fundingCycle;
    ```


6.  The upcoming funding cycle might also not yet be in storage, but might be reference-able.

    ```solidity
    // Get the queued funding cycle. It might exist but not yet have been stored.
    fundingCycle = queuedOf(_fundingCycleId);
    ```

    _Internal references:_

    * [`queuedOf`](queuedof.md)


7.  If the funding cycle ID being queried matches the queued funding cycle of the project, return it.

    ```solidity
    // If the IDs match, return it.
    if (fundingCycle.id == _fundingCycleId) return fundingCycle;
    ```


8.  If the ID being queried hasn't been found, return an empty Funding cycle struct.

    ```solidity
    // Return an empty Funding Cycle.
    return _getStructFor(0);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
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
  returns (JBFundingCycle memory fundingCycle)
{
  // The funding cycle should exist.
  require(_fundingCycleId > 0, '0x13 BAD_ID');

  // See if there's stored info for the provided ID.
  fundingCycle = _getStructFor(_fundingCycleId);
  
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
  return _getStructFor(0);
}
```
{% endtab %}

{% tab title="Errors" %}
| String             | Description                         |
| ------------------ | ----------------------------------- |
| **`0x13: BAD_ID`** | Thrown if the ID can't be resolved. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
