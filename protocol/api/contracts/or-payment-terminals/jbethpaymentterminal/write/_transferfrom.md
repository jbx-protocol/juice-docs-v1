# _transferFrom

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Transfers tokens.**

# Definition

```solidity
function _transferFrom(
  address,
  address payable _to,
  uint256 _amount
) internal override { ...}
```

* Arguments:
  * `_from` is the address from which the transfer should originate. This is ignored.
  * `_to` is the address to which the transfer should go.
  * `_amount` is the amount of the transfer, as a fixed point number with the same number of decimals as this terminal.
* The resulting function is internal to this contract and its inheriters.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.

#### Body

1.  Send the specified amount of ETH from this contract to the specified address.

    ```solidity
    Address.sendValue(_to, _amount);
    ```

    _Libraries used:_

    * [`Address`](https://docs.openzeppelin.com/contracts/2.x/api/utils#Address)
      * `.sendValue(...)`

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Transfers tokens.

  ignored: _from The address from which the transfer should originate.
  @param _to The address to which the transfer should go.
  @param _amount The amount of the transfer, as a fixed point number with the same number of decimals as this terminal.
*/
function _transferFrom(
  address,
  address payable _to,
  uint256 _amount
) internal override {
  Address.sendValue(_to, _amount);
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
