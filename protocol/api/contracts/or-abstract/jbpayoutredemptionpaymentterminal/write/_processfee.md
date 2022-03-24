# _processFee

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Process a fee of the specified amount.**

#### Definition

```solidity
function _processFee(uint256 _amount, address _beneficiary) { ... }
```

* Arguments:
  * `_amount` is the fee amount, as a floating point number with the same amount of decimals as this terminal.
  * `_beneficiary` is the address to mint the platform's tokens for.
* The function is private to this contract.
* The function doesn't return anything.

#### Body

1.  Get the terminal that the protocol project is accepting funds through for this terminal's token.

    ```solidity
    // Get the terminal for the protocol project.
    IJBPaymentTerminal _terminal = directory.primaryTerminalOf(_PROTOCOL_PROJECT_ID, token);
    ```

    _External references:_

    * [`primaryTerminalOf`](../../../jbdirectory/read/primaryterminalof.md)

2.  If the protocol's terminal is the same as this terminal, save gas by paying the contract internally.

    ```solidity
    // When processing the admin fee, save gas if the admin is using this contract as its terminal.
    if (_terminal == this) { ... }
    ```
    1.  Pay the protocol using the internal pay function.

        ```solidity
        _pay(_amount, address(this), _PROTOCOL_PROJECT_ID, _beneficiary, 0, false, '', bytes('')); // Use the local pay call.
        ```

        _Internal references:_

        * [`_pay`](_pay.md)

3.  Otherwise if the terminal is different, transfer the fee over.

    ```solidity
    else { ... }
    ```
    1.  Call any pre-transfer logic.

        ```solidity
        // Trigger any inherited pre-transfer logic.
        _beforeTransferTo(address(_terminal), _amount);
        ```

        _Virtual references:_

        * [`_beforeTransferTo`](_beforetransferto.md)

    4.  Get a reference to the ETH amount that should be attached to the transaction. Only attach anything if the token being paid is ETH.

        ```solidity
        // If this terminal's token is ETH, send it in msg.value.
        uint256 _payableValue = token == JBTokens.ETH ? _amount : 0;
        ```

    5.  Send the payment.

        ```solidity
        // Send the payment.
        _terminal.pay{value: _payableValue}(
          _amount,
          _PROTOCOL_PROJECT_ID,
          _beneficiary,
          0,
          false,
          '',
          bytes('')
        ); // Use the external pay call of the correct terminal.
        ```

        _External references:_

        * [`pay`](pay.md)

{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Process a fee of the specified amount.

  @param _amount The fee amount, as a floating point number with the same amount of decimals as this terminal.
  @param _beneficiary The address to mint the platform's tokens for.
*/
function _processFee(uint256 _amount, address _beneficiary) private {
  // Get the terminal for the protocol project.
  IJBPaymentTerminal _terminal = directory.primaryTerminalOf(_PROTOCOL_PROJECT_ID, token);

  // When processing the admin fee, save gas if the admin is using this contract as its terminal.
  if (_terminal == this)
    _pay(_amount, address(this), _PROTOCOL_PROJECT_ID, _beneficiary, 0, false, '', bytes('')); // Use the local pay call.
  else {
    // Trigger any inherited pre-transfer logic.
    _beforeTransferTo(address(_terminal), _amount);

    // If this terminal's token is ETH, send it in msg.value.
    uint256 _payableValue = token == JBTokens.ETH ? _amount : 0;

    // Send the payment.
    _terminal.pay{value: _payableValue}(
      _amount,
      _PROTOCOL_PROJECT_ID,
      _beneficiary,
      0,
      false,
      '',
      bytes('')
    ); // Use the external pay call of the correct terminal.
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String                | Description                             |
| --------------------- | --------------------------------------- |
| **`ZERO_VALUE_SENT`** | Thrown if the transaction had no value. |
{% endtab %}

{% tab title="Events" %}
| Name                                            | Data                                                                                                                                                             |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`AddToBalance`**](../events/addtobalance.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 value</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}

  