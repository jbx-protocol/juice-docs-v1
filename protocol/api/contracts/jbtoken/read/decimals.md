# decimals

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**The number of decimals included in the fixed point accounting of this token.**

### Definition

```solidity
function decimals() public view override(ERC20, IJBToken) returns (uint8) { ... }
```

* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBToken`](../../../interfaces/ijbtoken.md) interface.
* The function returns the number of decimals.

### Body

1.  Forward the call to the ERC20 implementation.

    ```solidity
    return super.decimals();
    ```

    _Inherited references:_

    * [`decimals`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#ERC20Detailed-decimals--)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  The number of decimals included in the fixed point accounting of this token.

  @return The number of decimals.
*/
function decimals() public view override(ERC20, IJBToken) returns (uint8) {
  return super.decimals();
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
