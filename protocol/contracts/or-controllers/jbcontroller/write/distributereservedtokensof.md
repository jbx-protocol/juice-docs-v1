# distributeReservedTokensOf

Contract:[`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Distributes all outstanding reserved tokens for a project.**

## Definition

```solidity
function distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  external
  nonReentrant
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the reserved tokens belong.
  * `_memo` is a memo to pass along to the emitted event.
* The function can be accessed externally by anyone.
* The function returns the amount of minted reserved tokens.

## Body

TODO
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Distributes all outstanding reserved tokens for a project.

  @param _projectId The ID of the project to which the reserved tokens belong.
  @param _memo A memo to pass along to the emitted event.

  @return The amount of minted reserved tokens.
*/
function distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  external
  nonReentrant
  returns (uint256)
{
  return _distributeReservedTokensOf(_projectId, _memo);
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
