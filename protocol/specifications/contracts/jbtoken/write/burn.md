# burn

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**Burn some outstanding tokens.**

_Only the owner of this contract cant burn some of its supply._

# Definition

```solidity
function burn(
  address _account,
  uint256 _amount,
  uint256
) external override onlyOwner { ... }
```

* Arguments:
  * `_account` is the account to burn tokens from.
  * `_amount` is the amount of tokens to burn.
* Through the `onlyOwner` modifier, this function can only be accessed by the address that owns this contract.
* The function overrides a function definition from the `IJBToken` interface.
* The function returns nothing.

# Body

1.  Forward the call to the internal version of the function.

    ```solidity
    return _burn(_account, _amount);
    ```

    _Internal references:_

    * [`_burn`](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#ERC20-_burn-address-uint256-)

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Burn some outstanding tokens.

  @dev
  Only the owner of this contract cant burn some of its supply.

  @param _account The account to burn tokens from.
  @param _amount The amount of tokens to burn.
*/
function burn(
  address _account,
  uint256 _amount,
  uint256
) external override onlyOwner {
  return _burn(_account, _amount);
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
