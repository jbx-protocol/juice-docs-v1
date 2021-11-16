# \_takeFee

Contract: [`JBETHPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Take a fee of the specified amount.**

# Definition

```solidity
function _takeFee(
  uint256 _amount,
  address _beneficiary,
  string memory _memo
) private { ... }
```

* Arguments:
  * `_amount` is the fee amount.
  * `_beneficiary` is the address to print the platforms tokens for.
  * `_memo` is a memo to pass along to the emitted event.
* The function is private to this contract.
* The function doesn't return anything.

# Body

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
    _terminal == this // Use the local pay call.
      ? _pay(_amount, 1, _beneficiary, 0, false, _memo, bytes('')) // Use the external pay call of the correct terminal.
      : _terminal.pay{value: _amount}(1, _beneficiary, 0, false, _memo, bytes(''));
    ```

    _Internal references:_

    * [`pay`](broken-reference)
    * [`_pay`](\_pay.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Take a fee of the specified amount.

  @param _amount The fee amount.
  @param _beneficiary The address to print the platforms tokens for.
  @param _memo A memo to pass along to the emitted event.
*/
function _takeFee(
  uint256 _amount,
  address _beneficiary,
  string memory _memo
) private {
  // Get the terminal for the JuiceboxDAO project.
  IJBTerminal _terminal = directory.primaryTerminalOf(1, token);

  // When processing the admin fee, save gas if the admin is using this contract as its terminal.
  _terminal == this // Use the local pay call.
    ? _pay(_amount, 1, _beneficiary, 0, false, _memo, bytes('')) // Use the external pay call of the correct terminal.
    : _terminal.pay{value: _amount}(1, _beneficiary, 0, false, _memo, bytes(''));
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
