# removeFromSetControllerAllowlist

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Remove a controller to the list of trusted controllers.**

_See `addToSetControllerAllowlist(...)` for context._

#### Definition

```solidity
function removeFromSetControllerAllowlist(address _address) external override onlyOwner { ... }
```

* Arguments:
  * `_address` is the allowed address to be removed.
* Through the `onlyOwner` modifier, this function can only be accessed by the address that owns this contract.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing.

#### Body

1.  Make sure the controller is on the allowlist.

    ```solidity
    // Check that the address is in the allowlist.
    if (!isAllowedToSetController[_address]) {
      revert CONTROLLER_NOT_IN_ALLOWLIST();
    }
    ```

    Internal references:

    * [`isAllowedToSetController`](properties/isallowedtosetcontroller.md)
2.  Add the controller to the allowlist.

    ```solidity
    // Remove the address from the allowlist.
    delete isAllowedToSetController[_address];
    ```

    Internal references:

    * [`isAllowedToSetController`](properties/isallowedtosetcontroller.md)
3.  Emit a `RemoveFromSetControllerAllowlist` event with the relevant parameters.

    ```solidity
    emit RemoveFromSetControllerAllowlist(_address, msg.sender);
    ```

    _Event references:_

    * [`RemoveFromSetControllerAllowlist`](../events/removefromsetcontrollerallowlist.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Remove a controller to the list of trusted controllers.

  @dev
  See `addToSetControllerAllowlist(...)` for context.

  @param _address The address to be removed.
*/
function removeFromSetControllerAllowlist(address _address) external override onlyOwner {
  // Check that the address is in the allowlist.
  if (!isAllowedToSetController[_address]) {
    revert CONTROLLER_NOT_IN_ALLOWLIST();
  }

  // Remove the address from the allowlist.
  delete isAllowedToSetController[_address];

  emit RemoveFromSetControllerAllowlist(_address, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                            | Description                                             |
| --------------------------------- | ------------------------------------------------------- |
| **`CONTROLLER_NOT_IN_ALLOWLIST`** | Thrown if a provided controller isn't on the allowlist. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                               | Data                                                                                          |
| ---------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [**`RemoveFromSetControllerAllowlist`**](../events/addtosetcontrollerallowlist.md) | <ul><li><code>address indexed controller</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
