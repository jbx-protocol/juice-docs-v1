# reclaimableOverflowOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflowed ETH that can be reclaimed by the specified number of tokens.**

_If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used._

#### Definition

```solidity
function reclaimableOverflowOf(uint256 _projectId, uint256 _tokenCount)
  external
  view
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get a reclaimable amount for.
  * `_tokenCount` is the number of tokens to make the calculation with.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function returns the amount of overflowed ETH that can be reclaimed.

#### Body

1.  Forward the call to the internal version of the function that is also used by other operations.

    ```solidity
    return _reclaimableOverflowOf(_projectId, fundingCycleStore.currentOf(_projectId), _tokenCount);
    ```

    _Internal references:_

    * [`_reclaimableOverflowOf`](\_reclaimableoverflowof.md)

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  The amount of overflowed ETH that can be reclaimed by the specified number of tokens.

  @dev If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used.

  @param _projectId The ID of the project to get a reclaimable amount for.
  @param _tokenCount The number of tokens to make the calculation with. 

  @return The amount of overflowed ETH that can be reclaimed.
*/
function reclaimableOverflowOf(uint256 _projectId, uint256 _tokenCount)
  external
  view
  returns (uint256)
{
  return _reclaimableOverflowOf(_projectId, fundingCycleStore.currentOf(_projectId), _tokenCount);
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
