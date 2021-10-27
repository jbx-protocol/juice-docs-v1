# reservedTokenBalanceOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Gets the amount of reserved tokens that a project has available to distribute.**

# Definition

```solidity
function reservedTokenBalanceOf(uint256 _projectId, uint256 _reservedRate)
  external
  view
  override
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get a reserved token balance of.
  * `_reservedRate` is the reserved rate to use when making the calculation.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns the reserved token balance.

# Body

TODO
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the amount of reserved tokens that a project has available to distribute.

  @param _projectId The ID of the project to get a reserved token balance of.
  @param _reservedRate The reserved rate to use when making the calculation.

  @return The current amount of reserved tokens.
*/
function reservedTokenBalanceOf(uint256 _projectId, uint256 _reservedRate)
  external
  view
  override
  returns (uint256)
{
  return
    _reservedTokenAmountFrom(
      _processedTokenTrackerOf[_projectId],
      _reservedRate,
      tokenStore.totalSupplyOf(_projectId)
    );
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
