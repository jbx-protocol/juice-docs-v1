# setFee

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Allows the fee to be updated for subsequent funding cycle configurations.**

_Only the owner of this contract can change the fee._

## Definition

```solidity
function setFee(uint256 _fee) external onlyOwner { ... }
```

* Arguments:
  * `_fee` is the new fee.
* Through the `onlyOwner` modifier, the function can only be accessed by the owner of this contract.
* The function doesn't return anything.

## Body

1.  Make sure the proposed fee is less than the max fee of 5%.

    ```solidity
    // The max fee is 5%.
    require(_fee <= 10, '0x36: BAD_FEE');
    ```
2.  Store the new fee.

    ```solidity
    // Store the new fee.
    fee = _fee;
    ```
3.  Emit a `SetFee` event with the relevant parameters.

    ```solidity
    emit SetFee(_fee, msg.sender);
    ```

    _Event references:_

    * [`SetFee`](../events/setfee.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Allows the fee to be updated for subsequent funding cycle configurations.

  @dev
  Only the owner of this contract can change the fee.

  @param _fee The new fee.
*/
function setFee(uint256 _fee) external onlyOwner {
  // The max fee is 5%.
  require(_fee <= 10, '0x36: BAD_FEE');

  // Store the new fee.
  fee = _fee;

  emit SetFee(_fee, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String              | Description                                    |
| ------------------- | ---------------------------------------------- |
| **`0x36: BAD_FEE`** | Thrown if the proposed fee is greater than 5%. |
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------ |
| [**`SetFee`**](../events/setfee.md) | <ul><li><code>uint256 fee</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
