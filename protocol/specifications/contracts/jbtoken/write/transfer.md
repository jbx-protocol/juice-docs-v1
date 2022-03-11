# transferFrom

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**Transfer tokens to an account.**

### Definition

```solidity
function transfer(
  uint256,
  address _to,
  uint256 _amount
) external override { ... }
```

* Arguments:
  * The `_projectId` parameter is ignored.
  * `_to` is the destination address.
  * `_amount` is the amount of the transfer, as a fixed point number with 18 decimals.
* The function overrides a function definition from the [`IJBToken`](../../../interfaces/ijbtoken.md) interface.
* The function doesn't return anything.

### Body

1.  Forward the call to the ERC20 implementation.

    ```solidity
    transfer(_to, _amount);
    ```

    _Inherited references:_

    * [`transfer`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#IERC20-transfer-address-uint256-)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Transfer tokens to an account.

  @param _to The destination address.
  @param _amount The amount of the transfer, as a fixed point number with 18 decimals.
*/
function transfer(
  uint256,
  address _to,
  uint256 _amount
) external override {
  transfer(_to, _amount);
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
