# totalSupply

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**The total supply of this ERC20.**

### Definition

```solidity
function totalSupply(uint256) external view override returns (uint256) { ... }
```

* Arguments:
  * The `_projectId` parameter is ignored.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBToken`](../../../interfaces/ijbtoken.md) interface.
* The function returns the total supply of this ERC20, as a fixed point number with 18 decimals.

### Body

1.  Forward the call to the ERC20 implementation.

    ```solidity
    return super.totalSupply();
    ```

    _Inherited references:_

    * [`totalSupply`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#IERC20-totalSupply--)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  The total supply of this ERC20.

  @return The total supply of this ERC20, as a fixed point number with 18 decimals.
*/
function totalSupply(uint256) external view override returns (uint256) {
  return super.totalSupply();
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
