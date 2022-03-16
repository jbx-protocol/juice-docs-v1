# delegate

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBTerminal`](../../../../interfaces/ijbterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**An address that serves as this terminal's delegate when making requests to juicebox ecosystem contracts.**

#### Definition

```solidity
function delegate() external view override returns (address) { ... }
```

* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The resulting function overrides a function definition from the `IJBTerminal` interface.
* The function returns the delegate address

#### Body

1.  The store will serve as this terminal's delegate, which will give it access to manipulate the funding cycle store and the token store on behalf of projects that use this terminal.

    ```solidity
    // The store is the delegate.
    return address(store);
    ```

    _Internal references:_

    * [`store`](../properties/store.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  An address that serves as this terminal's delegate when making requests to ecosystem contracts.

  @return The delegate address.
*/
function delegate() external view override returns (address) {
  // The store is the delegate.
  return address(store);
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
