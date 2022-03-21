# overflowAllowanceOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflow that a project is allowed to tap into on-demand throughout a configuration, and the currency it's in terms of.**

_The number of decimals in the returned fixed point amount is the same as that of the specified terminal._

### Definition

```solidity
function overflowAllowanceOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBPaymentTerminal _terminal
) external view override returns (uint256, uint256) { ... }
```

* Arguments:
* `_projectId` is the ID of the project to get the overflow allowance of.
* `_configuration` is the configuration of the during which the allowance applies.
* `_terminal` is the [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md) managing the overflow.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns the overflow allowance and the currency of the overflow allowance.

### Body

1.  Get a reference to the packed overflow allowance data.

    ```solidity
    // Get a reference to the packed data.
    uint256 _data = _packedOverflowAllowanceDataOf[_projectId][_configuration][_terminal];
    ```

    _Internal references:_

    * [`_packedOverflowAllowanceDataOf`](../properties/\_packedoverflowallowancedataof.md)
2.  Return the overflow allowance, which is in the first 248 bits, and the currency the overflow allowance is in terms of, which is in the last 8 bits.

    ```solidity
    // The allowance is in bits 0-247. The currency is in bits 248-255.
    return (uint256(uint248(_data)), _data >> 248);
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  The amount of overflow that a project is allowed to tap into on-demand throughout a configuration, and the currency it's in terms of.

  @dev
  The number of decimals in the returned fixed point amount is the same as that of the specified terminal. 

  @param _projectId The ID of the project to get the overflow allowance of.
  @param _configuration The configuration of the during which the allowance applies.
  @param _terminal The terminal managing the overflow.

  @return The overflow allowance, as a fixed point number with the same number of decimals as the provided terminal.
  @return The currency of the overflow allowance.
*/
function overflowAllowanceOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBPaymentTerminal _terminal
) external view override returns (uint256, uint256) {
  // Get a reference to the packed data.
  uint256 _data = _packedOverflowAllowanceDataOf[_projectId][_configuration][_terminal];

  // The allowance is in bits 0-247. The currency is in bits 248-255.
  return (uint256(uint248(_data)), _data >> 248);
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
