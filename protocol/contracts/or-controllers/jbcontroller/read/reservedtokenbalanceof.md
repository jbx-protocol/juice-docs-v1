# reservedTokenBalanceOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

{% tabs %}
{% tab title="Step by step" %}
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

{% tab title="Errors" %}
| String                  | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| **`0x0f: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
{% endtab %}

{% tab title="Events" %}
| Name                                    | Data                                                                                                                                                                                                                 |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetSplit`**](../events/setsplit.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><code>Split split</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
