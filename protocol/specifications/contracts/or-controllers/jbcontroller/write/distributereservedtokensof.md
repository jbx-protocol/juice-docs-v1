# distributeReservedTokensOf

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Distributes all outstanding reserved tokens for a project.**

# Definition

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
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The function returns the amount of minted reserved tokens.

# Body

1.  Forward the call to the internal version of the function that is also used by other operations.

    ```solidity
    return _distributeReservedTokensOf(_projectId, _memo);
    ```

    _Internal references:_

    * [`_distributeReservedTokensOf`](\_distributereservedtokensof.md)
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

{% tab title="Events" %}
| Name                                                                                | Data                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DistributeReservedTokens`**](../events/distributereservedtokens.md)             | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed beneficiary</code></li><li><code>uint256 count</code></li><li><code>uint256 projectOwnerTokenCount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
| [**`DistributeToReservedTokenSplit`**](../events/distributetoreservedtokensplit.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><a href="../../../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>split</code></li><li><code>uint256 tokenCount</code></li><li><code>address caller</code></li></ul>                              |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
