# setFeeGauge

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Allows the fee gauge to be updated.**

_Only the owner of this contract can change the fee gauge._

## Definition

```solidity
function setFeeGauge(IJBFeeGauge _feeGauge) external onlyOwner { ... }
```

* Arguments:
  * `_feeGauge` is the new fee gauge.
* Through the `onlyOwner` modifier, the function can only be accessed by the owner of this contract.
* The function doesn't return anything.

## Body

1.  Store the new fee gauge.

    ```solidity
    // Store the new fee gauge.
    feeGauge = _feeGauge;
    ```
2.  Emit a `SetFeeGauge` event with the relevant parameters.

    ```solidity
    emit SetFeeGauge(_feeGauge, msg.sender);
    ```

    _Event references:_

    * [`SetFeeGauge`](../events/setfeegauge.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Allows the fee gauge to be updated.

  @dev
  Only the owner of this contract can change the fee gauge.

  @param _feeGauge The new fee gauge.
*/
function setFeeGauge(IJBFeeGauge _feeGauge) external onlyOwner {
  // Store the new fee gauge.
  feeGauge = _feeGauge;

  emit SetFeeGauge(_feeGauge, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------ |
| [**`SetFeeGauge`**](../events/setfeegauge.md) | <ul><li><a href="../../../../interfaces/ijbfeegauge.md"><code>IJBFeeGauge</code></a><code>feeGauge</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
