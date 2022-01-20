# overflowAllowanceOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflow that a project is allowed to tap into on-demand throughout configuration.**

## Definition

```solidity
function overflowAllowanceOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) { ... }
```

* Arguments:
* `_projectId` is the ID of the project to get the overflow allowance of.
* `_configuration` is the configuration of the during which the allowance applies.
* `_terminal` is the [`IJBTerminal`](../../../../interfaces/ijbterminal.md) managing the overflow.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns the overflow allowance.

## Body

1.  Return the first 248 bits of the packed overflow allowance data.

    ```solidity
    return uint256(uint248(_packedOverflowAllowanceDataOf[_projectId][_configuration][_terminal]));
    ```

    _Internal references:_

    * [`_packedOverflowAllowanceDataOf`](../properties/\_packedoverflowallowancedataof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  The amount of overflow that a project is allowed to tap into on-demand throughout configuration.

  @param _projectId The ID of the project to get the overflow allowance of.
  @param _configuration The configuration of the during which the allowance applies.
  @param _terminal The terminal managing the overflow.
*/
function overflowAllowanceOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) {
  return uint256(uint248(_packedOverflowAllowanceDataOf[_projectId][_configuration][_terminal]));
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
