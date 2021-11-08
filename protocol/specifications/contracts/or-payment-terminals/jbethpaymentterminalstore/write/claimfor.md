# claim

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Allows this store to be claimed by an address so that it recognized the address as its terminal.**

## Definition

```solidity
function claimFor(IJBTerminal _terminal) external { ... }
```

* The function can be accessed externally by anyone.
* The function doesn't return anything.

## Body

1.  Make sure the contract hasn't already been claimed.

    ```solidity
    // This store can only be claimed once.
    require(terminal == IJBTerminal(address(0)), '0x4b: ALREADY_CLAIMED');
    ```

    _Internal references:_

    * [`terminal`](../properties/terminal.md)
2.  Store the terminal as the address claiming this store.

    ```solidity
    // Set the terminal.
    terminal = _terminal;
    ```

    _Internal references:_

    * [`terminal`](../properties/terminal.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Allows this store to be claimed by an address so that it recognized the address as its terminal.
*/
function claimFor(IJBTerminal _terminal) external {
  // This store can only be claimed once.
  require(terminal == IJBTerminal(address(0)), '0x4b: ALREADY_CLAIMED');

  // Set the terminal.
  terminal = _terminal;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                      | Description                                      |
| --------------------------- | ------------------------------------------------ |
| **`0x4b: ALREADY_CLAIMED`** | Thrown if the contract has already been claimed. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
