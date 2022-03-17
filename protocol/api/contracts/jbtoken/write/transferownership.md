# transferOwnership

Contract: [`JBToken`](../)​‌

Interface: [`IJBToken`](../../../interfaces/ijbtoken.md)

{% tabs %}
{% tab title="Step by step" %}
**Transfer ownership of this contract to another address.**

_Only the owner of this contract can transfer it._

### Definition

```solidity
function transferOwnership(address _newOwner)
  public
  virtual
  override(Ownable, IJBToken)
  onlyOwner { ... }
```

* Arguments:
  * `_newOwner` is the new owner.
* Through the [`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--) modifier, this function can only be accessed by the address that owns this contract.
* The function overrides a function definition from the [`IJBToken`](../../../interfaces/ijbtoken.md) interface.
* The function returns nothing.

### Body

1.  Forward the call to the inherited version from `Ownable`.

    ```solidity
    return super.transferOwnership(_newOwner);
    ```

    _Inherited references:_

    * [`transferOwnership`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-transferOwnership-address-)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Transfer ownership of this contract to another address.

  @dev
  Only the owner of this contract can transfer it.

  @param _newOwner The new owner.
*/
function transferOwnership(address _newOwner)
  public
  virtual
  override(Ownable, IJBToken)
  onlyOwner
{
  return super.transferOwnership(_newOwner);
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
