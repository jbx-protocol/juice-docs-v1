# ethBalanceOf

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../interfaces/ijbethterminalof.md)

{% tabs %}
{% tab title="Step by step" %}
**The ETH balance that this terminal holds for each project.**

## Definition

```solidity
function ethBalanceOf(uint256 _projectId) external view override returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the ETH balance belongs.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
* The function returns the delegate address

## Body

1.  Since this terminal stores ETH, simply return the project's ETH balance.

    ```solidity
    // The store's balance is already in ETH.
    return store.balanceOf(_projectId);
    ```

    _External references:_

    * [`balanceOf`](../../jbethpaymentterminalstore/read/balanceof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The ETH balance that this terminal holds for each project.

  @param _projectId The ID of the project to which the balance belongs.

  @return The ETH balance.
*/
function ethBalanceOf(uint256 _projectId) external view override returns (uint256) {
  // The store's balance is already in ETH.
  return store.balanceOf(_projectId);
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
