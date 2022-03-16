# distributionLimitOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**The amount of token that a project can withdraw per funding cycle.**

### Definition

```solidity
function distributionLimitOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) { ... }
```

* Arguments:
* `_projectId` is the ID of the project to get the distribution limit of.
* `_configuration` is the configuration during which the distribution limit applies.
* `_terminal` is the [`IJBTerminal`](../../../../interfaces/ijbterminal.md) from which distributions are being limited.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns the distribution limit.

### Body

1.  Return the first 248 bits of the packed distribution limit data.

    ```solidity
    return uint256(uint248(_packedDistributionLimitDataOf[_projectId][_configuration][_terminal]));
    ```

    _Internal references:_

    * [`_packedDistributionLimitDataOf`](../properties/\_packeddistributionlimitdataof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  The amount of token that a project can withdraw per funding cycle.

  @param _projectId The ID of the project to get the distribution limit of.
  @param _configuration The configuration during which the distribution limit applies.
  @param _terminal The terminal from which distributions are being limited. 
*/
function distributionLimitOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) {
  return uint256(uint248(_packedDistributionLimitDataOf[_projectId][_configuration][_terminal]));
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
