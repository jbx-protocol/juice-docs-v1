# reservedTokenBalanceOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

{% tabs %}
{% tab title="Step by step" %}
**Get all splits for the specified project ID, within the specified domain, for the specified group.**

## Definition

```solidity
function splitsOf(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group
) external view override returns (JBSplit[] memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get splits for.
  * `_domain` is an identifier within which the returned splits should be considered active.
  * `_group` is the identifying group of the splits.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBSplitStore` interface.
* The function returns an array of [`JBSplit`](../../../data-structures/jbsplit.md)s.

## Body
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
