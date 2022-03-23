# addToBalanceOf

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Receives funds belonging to the specified project.**

#### Definition

```solidity
function addToBalanceOf(
  uint256 _projectId,
  uint256 _amount,
  string calldata _memo
) external payable virtual override isTerminalOf(_projectId) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funds received belong.
  * `_amount` is the amount of tokens to add, as a fixed point number with the same number of decimals as this terminal. If this is an ETH terminal, this is ignored and msg.value is used instead.
  * `_memo` is a memo to pass along to the emitted event.
* The function can be accessed externally by anyone.
* Through the [`isTerminalOf`](../modifiers/isterminalof.md) modifier, this transaction reverts if this terminal is not one of the project's terminals.
* The function accepts ETH. The transaction reverts if receives ETH but the terminal's token type isn't ETH.
* The function doesn't return anything.

#### Body

1.  If this terminal's token isn't ETH, make sure ETH wasn't sent to the function, then transfer the amount of tokens from the message sender to this contract. If this terminal's contract is ETH, override the specified amount value with with amount of ETH sent to the function.

    ```solidity
    // If this terminal's token isn't ETH, make sure no msg.value was sent, then transfer the tokens in from msg.sender.
    if (token != JBTokens.ETH) {
      // Amount must be greater than 0.
      if (msg.value > 0) revert NO_MSG_VALUE_ALLOWED();

      // Transfer tokens to this terminal from the msg sender.
      _transferFrom(msg.sender, payable(address(this)), _amount);
    }
    // If the terminal's token is ETH, override `_amount` with msg.value.
    else _amount = msg.value;
    ```
2.  Record the added balance.

    ```solidity
    // Record the added funds.
    _addToBalance(_amount, _projectId, _memo);
    ```

    _Internal references:_

    * [`_addToBalance`](_addtobalance.md)
3.  Refund any held fees. This is useful to allow a project to withdraw funds from the protocol and subsequently add them back without paying eventually having to pay double fees.

    ```solidity
    // Refund any held fees to make sure the project doesn't pay double for funds going in and out of the protocol.
    _refundHeldFees(_projectId, msg.value);
    ```

    _Internal references:_

    * [`_refundHeldFees`](\_refundheldfees.md)
4.  Emit a `AddToBalance` event with the relevant parameters.

    ```solidity
    emit AddToBalance(_projectId, msg.value, _memo, msg.sender);
    ```

    _Event references:_

    * [`AddToBalance`](../events/addtobalance.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Receives funds belonging to the specified project.

  @param _projectId The ID of the project to which the funds received belong.
  @param _amount The amount of tokens to add, as a fixed point number with the same number of decimals as this terminal. If this is an ETH terminal, this is ignored and msg.value is used instead.
  @param _memo A memo to pass along to the emitted event.
*/
function addToBalanceOf(
  uint256 _projectId,
  uint256 _amount,
  string calldata _memo
) external payable virtual override isTerminalOf(_projectId) {
  // If this terminal's token isn't ETH, make sure no msg.value was sent, then transfer the tokens in from msg.sender.
  if (token != JBTokens.ETH) {
    // Amount must be greater than 0.
    if (msg.value > 0) revert NO_MSG_VALUE_ALLOWED();

    // Transfer tokens to this terminal from the msg sender.
    _transferFrom(msg.sender, payable(address(this)), _amount);
  }
  // If the terminal's token is ETH, override `_amount` with msg.value.
  else _amount = msg.value;

  _addToBalance(_amount, _projectId, _memo);
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
