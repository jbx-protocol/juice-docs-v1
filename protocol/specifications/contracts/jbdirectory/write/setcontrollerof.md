# setControllerOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Update the controller that manages how terminals interact with the ecosystem.**

_A controller can be set if:_

* the message sender is the project owner or an operator having the correct authorization.
* or, an allowedlisted address is setting an allowlisted controller.

#### Definition

```solidity
function setControllerOf(uint256 _projectId, IJBController _controller)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_CONTROLLER,
    (address(controllerOf[_projectId]) == address(0) && msg.sender == address(_controller)) ||
      address(controllerOf[_projectId]) == msg.sender
  ) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to set a new controller for.
  * `_controller` is the new controller to set.
* Through the [`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.SET_CONTROLLER` permission by the project owner for the provided `_projectId`, or from an allow-listed controller if the new controller being set is also an allow-listed controller.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns nothing. (\_setControllerAllowlist\[address(\_controller)] && \_setControllerAllowlist\[msg.sender])

#### Body

1.  Make sure the provided controller isn't the zero address.

    ```solidity
    // Can't set the zero address.
    if (_controller == IJBController(address(0))) {
      revert SET_CONTROLLER_ZERO_ADDRESS();
    }
    ```
2.  Make sure the provided controller isn't already set.

    ```solidity
    // Can't set the controller if it's already set.
    if (controllerOf[_projectId] == _controller) {
      revert SET_CONTROLLER_ALREADY_SET();
    }
    ```

    Internal references:

    * [`controllerOf`](../properties/controllerof.md)
3.  Project IDs are assigned incrementally. If the provided `_projectId` is greater than the number of projects, it must not be a valid project ID.

    ```solidity
    // The project must exist.
    if (projects.count() < _projectId) {
      revert INVALID_PROJECT_ID();
    }
    ```

    Internal references:

    * [`projects`](../properties/projects.md)
4.  Store the provided controller as the `controllerOf` the project.

    ```solidity
    // Set the new controller.
    controllerOf[_projectId] = _controller;
    ```

    Internal references:

    * [`controllerOf`](../properties/controllerof.md)
5.  Emit a `SetController` event with the relevant parameters.

    ```solidity
    emit SetController(_projectId, _controller, msg.sender);
    ```

    _Event references:_

    * [`SetController`](../events/setcontroller.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Update the controller that manages how terminals interact with the ecosystem.

  @dev 
  A controller can be set if:
    - the message sender is the project owner or an operator having the correct authorization.
    - or, an allowedlisted address is setting an allowlisted controller.

  @param _projectId The ID of the project to set a new controller for.
  @param _controller The new controller to set.
*/
function setControllerOf(uint256 _projectId, IJBController _controller)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_CONTROLLER,
    (isAllowedToSetController[address(_controller)] && isAllowedToSetController[msg.sender]) 
{
  // Can't set the zero address.
  if (_controller == IJBController(address(0))) {
    revert SET_CONTROLLER_ZERO_ADDRESS();
  }

  // Can't set the controller if it's already set.
  if (controllerOf[_projectId] == _controller) {
    revert SET_CONTROLLER_ALREADY_SET();
  }

  // The project must exist.
  if (projects.count() < _projectId) {
    revert INVALID_PROJECT_ID();
  }

  // Set the new controller.
  controllerOf[_projectId] = _controller;

  emit SetController(_projectId, _controller, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                            | Description                                                      |
| --------------------------------- | ---------------------------------------------------------------- |
| **`SET_CONTROLLER_ZERO_ADDRESS`** | Thrown if the provided controller is the zero address.           |
| **`CONTROLLER_ALREADY_SET`**      | Thrown if the provided controller is already the set controller. |
| **`INVALID_PROJECT_ID`**          | Thrown if the provided project doesn't yet exist.                |
{% endtab %}

{% tab title="Events" %}
| Name                                              | Data                                                                                                                                                                                                             |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetController`**](../events/setcontroller.md) | <ul><li><code>int256 indexed projectId</code></li><li><a href="../../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>indexed controller</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
