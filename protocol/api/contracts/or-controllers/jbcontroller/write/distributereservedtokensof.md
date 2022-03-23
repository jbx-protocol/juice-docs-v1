# distributeReservedTokensOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Distributes all outstanding reserved tokens for a project.**

### Definition

```solidity
function distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  external
  override
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the reserved tokens belong.
  * `_memo` is a memo to pass along to the emitted event.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns the amount of minted reserved tokens.

### Body

1.  Forward the call to the internal version of the function that is also used by other operations.

    ```solidity
    return _distributeReservedTokensOf(_projectId, _memo);
    ```

    _Internal references:_

    * [`_distributeReservedTokensOf`](_distributereservedtokensof.md)
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
  override
  returns (uint256)
{
  return _distributeReservedTokensOf(_projectId, _memo);
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
