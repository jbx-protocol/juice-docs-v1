# addToSetControllerAllowlist

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Add a controller to the list of trusted controllers.**

_The owner (Juicebox multisig) can add addresses which are allowed to change a project's controller._ _Those addresses are known and vetted controllers as well as contracts designed to launch new projects._ _This is not a requirement for all controllers. However, unknown controllers may require additional transactions to perform certain operations._

_If you would like an address/contract allowlisted, please reach out to JuiceboxDAO._

#### Definition

```solidity
function addToSetControllerAllowlist(address _address) external override onlyOwner { ... }
```

* Arguments:
  * `_address` is the allowed address to be added.
* Through the `onlyOwner` modifier, this function can only be accessed by the address that owns this contract.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing.

#### Body

1.  Make sure the controller isn't already on the allowlist.

    ```solidity
    // Check that the address is not already in the allowlist.
    if (isAllowedToSetController[_address]) {
      revert CONTROLLER_ALREADY_IN_ALLOWLIST();
    }
    ```

    Internal references:

    * [`isAllowedToSetController`](properties/isallowedtosetcontroller.md)
2.  Add the controller to the allowlist.

    ```solidity
    // Add the address to the allowlist.
    isAllowedToSetController[_address] = true;
    ```

    Internal references:

    * [`isAllowedToSetController`](properties/isallowedtosetcontroller.md)
3.  Emit a `AddToSetControllerAllowlist` event with the relevant parameters.

    ```solidity
    emit AddToSetControllerAllowlist(_address, msg.sender);
    ```

    _Event references:_

    * [`AddToSetControllerAllowlist`](../events/addtosetcontrollerallowlist.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Add a controller to the list of trusted controllers.

  @dev
  The owner (Juicebox multisig) can add addresses which are allowed to change
  a project's controller. Those addresses are known and vetted controllers as well as
  contracts designed to launch new projects. This is not a requirement for all controllers.
  However, unknown controllers may require additional transactions to perform certain operations.

  @dev
  If you would like an address/contract allowlisted, please reach out to JuiceboxDAO.

  @param _address the allowed address to be added.
*/
function addToSetControllerAllowlist(address _address) external override onlyOwner {
  // Check that the address is not already in the allowlist.
  if (isAllowedToSetController[_address]) {
    revert CONTROLLER_ALREADY_IN_ALLOWLIST();
  }

  // Add the address to the allowlist.
  isAllowedToSetController[_address] = true;

  emit AddToSetControllerAllowlist(_address, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                                | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| **`CONTROLLER_ALREADY_IN_ALLOWLIST`** | Thrown if a provided controller is already on the allowlist. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                          | Data                                                                                          |
| ----------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [**`AddToSetControllerAllowlist`**](../events/addtosetcontrollerallowlist.md) | <ul><li><code>address indexed controller</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
