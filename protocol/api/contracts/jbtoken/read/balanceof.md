# balanceOf

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**An account's balance of this ERC20.**

### Definition

```solidity
function balanceOf(address _account, uint256) external view override returns (uint256) { ... }
```

* Arguments:
  * `_account` is the account to get a balance of.
  * The `_projectId` parameter is ignored.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBToken`](../../../interfaces/ijbtoken.md) interface.
* The function returns the balance of the `_account` of this ERC20, as a fixed point number with 18 decimals.

### Body

1.  Forward the call to the ERC20 implementation.

    ```solidity
    return super.balanceOf(_account);
    ```

    _Inherited references:_

    * [`balanceOf`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#IERC20-balanceOf-address-)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  An account's balance of this ERC20.

  @param _account The account to get a balance of.

  @return The balance of the `_account` of this ERC20, as a fixed point number with 18 decimals.
*/
function balanceOf(address _account, uint256) external view override returns (uint256) {
  return super.balanceOf(_account);
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
