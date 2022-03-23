# distributionLimitOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**The amount of token that a project can distribute per funding cycle, and the currency it's in terms of.**

_The number of decimals in the returned fixed point amount is the same as that of the specified terminal._

### Definition

```solidity
function distributionLimitOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBPaymentTerminal _terminal
) external view override returns (uint256, uint256) { ... }
```

* Arguments:
* `_projectId` is the ID of the project to get the distribution limit of.
* `_configuration` is the configuration during which the distribution limit applies.
* `_terminal` is the [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md) from which distributions are being limited.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns:
  * `distributionLimit` is the distribution limit, as a fixed point number with the same number of decimals as the provided terminal.
  * `distributionLimitCurrency` is the currency from [`JBCurrencies`](../../../../libraries/jbcurrencies.md) that the returned distribution limit is in terms of.

### Body

1.  Get a reference to the packed distribution limit data.

    ```solidity
    // Get a reference to the packed data.
    uint256 _data = _packedDistributionLimitDataOf[_projectId][_configuration][_terminal];
    ```

    _Internal references:_

    * [`_packedDistributionLimitDataOf`](../properties/_packeddistributionlimitdataof.md)
2.  Return the distribution limit, which is in the first 248 bits, and the currency the distribution limit is in terms of, which is in the last 8 bits.

    ```solidity
    // The limit is in bits 0-247. The currency is in bits 248-255.
    return (uint256(uint248(_data)), _data >> 248);
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  The amount of token that a project can distribute per funding cycle, and the currency it's in terms of.

  @dev
  The number of decimals in the returned fixed point amount is the same as that of the specified terminal. 

  @param _projectId The ID of the project to get the distribution limit of.
  @param _configuration The configuration during which the distribution limit applies.
  @param _terminal The terminal from which distributions are being limited.

  @return The distribution limit, as a fixed point number with the same number of decimals as the provided terminal.
  @return The currency of the distribution limit.
*/
function distributionLimitOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBPaymentTerminal _terminal
) external view override returns (uint256, uint256) {
  // Get a reference to the packed data.
  uint256 _data = _packedDistributionLimitDataOf[_projectId][_configuration][_terminal];

  // The limit is in bits 0-247. The currency is in bits 248-255.
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
