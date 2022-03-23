# recordAddedBalanceFor

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Records newly added funds for the project.**

_The msg.sender must be an [`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)._
#### Definition

```solidity
function recordAddedBalanceFor(uint256 _projectId, uint256 _amount)
  external
  override
  nonReentrant { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funds being added belong.
  * `_amount` is the amount of temrinal tokens added, as a fixed point number with the same amount of decimals as its relative terminal.
* The resulting function overrides a function definition from the [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md) interface.
* The function doesn't return anything.

#### Body

1.  Increment the project's balance by the specified amount.

    ```solidity
    // Increment the balance.
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
      balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] +
      _amount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly added funds for the project.

  @dev
  The msg.sender must be an IJBPaymentTerminal. 

  @param _projectId The ID of the project to which the funds being added belong.
  @param _amount The amount of temrinal tokens added, as a fixed point number with the same amount of decimals as its relative terminal.
*/
function recordAddedBalanceFor(uint256 _projectId, uint256 _amount)
  external
  override
  nonReentrant
{
  // Increment the balance.
  balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] +
    _amount;
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
