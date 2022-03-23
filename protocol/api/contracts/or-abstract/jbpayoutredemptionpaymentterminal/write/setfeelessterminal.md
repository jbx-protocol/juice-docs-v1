# setFeelessTerminal

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Sets whether projects operating on this terminal can pay projects operating on the specified terminal without incurring a fee.**

_Only the owner of this contract can set terminal's as feeless._

#### Definition

```solidity
function setFeelessTerminal(IJBPaymentTerminal _terminal, bool _flag)
  external
  virtual
  override
  onlyOwner { ... }
```

* Arguments:
  * `_terminal` is the [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md) that can be paid towards while still bypassing fees.
  * `_flag` is a flag indicating whether the terminal should be feeless or not.
* Through the [`onlyOwner`](https://docs.openzeppelin.com/contracts/2.x/api/ownership#Ownable-onlyOwner--) modifier, the function can only be accessed by the owner of this contract.
* The function doesn't return anything.

#### Body

1.  Store the flag for the terminal.

    ```solidity
    // Set the flag value.
    isFeelessTerminal[_terminal] = _flag;
    ```
2.  Emit a `SetFeelessTerminal` event with the relevant parameters.

    ```solidity
    emit SetFeelessTerminal(_terminal, _flag, msg.sender);
    ```

    _Event references:_

    * [`SetFeelessTerminal`](../events/setfeelessterminal.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Sets whether projects operating on this terminal can pay projects operating on the specified terminal without incurring a fee.

  @dev
  Only the owner of this contract can set terminal's as feeless.

  @param _terminal The terminal that can be paid towards while still bypassing fees.
  @param _flag A flag indicating whether the terminal should be feeless or not.
*/
function setFeelessTerminal(IJBPaymentTerminal _terminal, bool _flag)
  external
  virtual
  override
  onlyOwner
{
  // Set the flag value.
  isFeelessTerminal[_terminal] = _flag;

  emit SetFeelessTerminal(_terminal, _flag, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                          | Data                                                                                                                                                    |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetFeelessTerminal`**](../events/setfeelessterminal.md) | <ul><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)indexed terminal</code></li><li><code>bool indexed flag</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
