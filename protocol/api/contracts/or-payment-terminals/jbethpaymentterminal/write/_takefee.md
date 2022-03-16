# \_takeFee

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Take a fee of the specified amount.**

#### Definition

```solidity
function _takeFee(uint256 _amount, address _beneficiary) private { ... }
```

* Arguments:
  * `_amount` is the fee amount.
  * `_beneficiary` is the address to print the platforms tokens for.
* The function is private to this contract.
* The function doesn't return anything.

#### Body

1.  Get a reference to the protocol project's primary ETH terminal. The project's project has an ID of 1.

    ```solidity
    // Get the terminal for the protocol project.
    IJBTerminal _terminal = directory.primaryTerminalOf(1, token);
    ```

    _External references:_

    * [`primaryTerminalOf`](../../../jbdirectory/read/primaryterminalof.md)
2.  Make a payment to the protocol project. If it's primary ETH terminal is this contract, save gas by just calling the internal `_pay` function. Otherwise, call the terminal's external `pay` function.

    ```solidity
    // When processing the admin fee, save gas if the admin is using this contract as its terminal.
    _terminal == this
      ? _pay(_amount, address(this), 1, _beneficiary, 0, false, '', bytes('')) // Use the local pay call.
      : _terminal.pay{value: _amount}(1, _beneficiary, 0, false, '', bytes('')); // Use the external pay call of the correct terminal.
    ```

    _Internal references:_

    * [`pay`](pay.md)
    * [`_pay`](\_pay.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Take a fee of the specified amount.

  @param _amount The fee amount.
  @param _beneficiary The address to print the platforms tokens for.
*/
function _takeFee(uint256 _amount, address _beneficiary) private {
  // Get the terminal for the protocol project.
  IJBTerminal _terminal = directory.primaryTerminalOf(1, token);

  // When processing the admin fee, save gas if the admin is using this contract as its terminal.
  _terminal == this
    ? _pay(_amount, address(this), 1, _beneficiary, 0, false, '', bytes('')) // Use the local pay call.
    : _terminal.pay{value: _amount}(1, _beneficiary, 0, false, '', bytes('')); // Use the external pay call of the correct terminal.
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
