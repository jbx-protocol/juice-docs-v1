# _contains

{% tabs %}
{% tab title="Step by step" %}
**Check if the provided terminal array contains the provided terminal.**

### Definition

```solidity
function _contains(IJBPaymentTerminal[] calldata _terminals, IJBPaymentTerminal _terminal)
  private
  pure
  returns (bool) { ... }
```

* Arguments:
  * `_terminals` is the terminals to look through.
  * `_terminal` is the terminal to check for.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns whether or not the `_terminals` includes the `_terminal`. 

### Body

1.  Loop through each of the provided terminals looking for the provided terminal. If it's found, return true.

    ```solidity
    for (uint256 _i; _i < _terminals.length; _i++) if (_terminals[_i] == _terminal) return true;
    ```

2.  If the terminal is not found, return false.

    ```solidity
    return false;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Check if the provided terminal array contains the provided terminal.

  @param _terminals The terminals to look through.
  @param _terminal The terminal to check for.

  @return Whether or not the `_terminals` includes the `_terminal`.
*/
function _contains(IJBPaymentTerminal[] calldata _terminals, IJBPaymentTerminal _terminal)
  private
  pure
  returns (bool)
{
  for (uint256 _i; _i < _terminals.length; _i++) if (_terminals[_i] == _terminal) return true;
  return false;
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
