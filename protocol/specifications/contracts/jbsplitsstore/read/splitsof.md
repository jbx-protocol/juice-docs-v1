# splitsOf

Contract: [`JBSplitsStore`](../)​‌

Interface: [`IJBSplitsStore`](../../../interfaces/ijbsplitsstore.md)

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
* The function overrides a function definition from the `IJBSplitsStore` interface.
* The function returns an array of [`JBSplit`](../../../data-structures/jbsplit.md)s.

## Body

1.  This function just reads and returns the splits of the project, within the specified domain, for the specified group.

    ```solidity
    return _getStructsFor(_projectId, _domain, _group);
    ```

    Internal references:

    * [`_getStructsFor`](../properties/\_getstructsfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Get all splits for the specified project ID, within the specified domain, for the specified group.

  @param _projectId The ID of the project to get splits for.
  @param _domain An identifier within which the returned splits should be considered active.
  @param _group The identifying group of the splits.

  @return An array of all splits for the project.
*/
function splitsOf(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group
) external view override returns (JBSplit[] memory) {
  return _getStructsFor(_projectId, _domain, _group);
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
