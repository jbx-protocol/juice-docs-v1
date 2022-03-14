# approve

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**Approves an account to spend tokens on the `msg.sender`s behalf.**

### Definition

```solidity
function approve(
  uint256,
  address _spender,
  uint256 _amount
) external override { ... }
```

* Arguments:
  * The `_projectId` parameter is ignored.
  * `_spender` is the address that will be spending tokens on the `msg.sender`s behalf.
  * `_amount` is the amount the `_spender` is allowed to spend.
* The function overrides a function definition from the [`IJBToken`](../../../interfaces/ijbtoken.md) interface.
* The function doesn't return anything.

### Body

1.  Forward the call to the ERC20 implementation.

    ```solidity
    approve(_spender, _amount);
    ```

    _Inherited references:_

    * [`_mint`](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#IERC20-approve-address-uint256-)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Approves an account to spend tokens on the `msg.sender`s behalf.

  @param _spender The address that will be spending tokens on the `msg.sender`s behalf.
  @param _amount The amount the `_spender` is allowed to spend.
*/
function approve(
  uint256,
  address _spender,
  uint256 _amount
) external override {
  approve(_spender, _amount);
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
