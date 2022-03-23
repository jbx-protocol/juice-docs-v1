# setControllerOf

Contract:[`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

{% tabs %}
{% tab title="Step by step" %}
**Update the controller that manages how terminals interact with the ecosystem.**

_A controller can be set if:_

* the message sender is the project owner or an operator having the correct authorization.
* the message sender is the project's current controller.
* or, an allowedlisted address is setting a controller for a project that doesn't already have a controller.

#### Definition

```solidity
function setControllerOf(uint256 _projectId, IJBController _controller)
  external
  override
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.SET_CONTROLLER,
    (msg.sender == address(controllerOf[_projectId]) ||
      (isAllowedToSetFirstController[msg.sender] &&
        controllerOf[_projectId] == IJBController(address(0))))
  ) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to set a new controller for.
  * `_controller` is the new controller to set.
* Through the [`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the [`JBOperations.SET_CONTROLLER`](../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`, from the project's current controller, or from an allow-listed controller if the project doesn't already have a controller set.
* The function overrides a function definition from the [`IJBDirectory`](../../../interfaces/ijbdirectory.md) interface.
* The function doesn't return anything

#### Body

1.  Project IDs are assigned incrementally. If the provided project ID is greater than the number of projects, it must not be a valid project ID.

    ```solidity
    // The project must exist.
    if (projects.count() < _projectId) revert INVALID_PROJECT_ID_IN_DIRECTORY();
    ```

    _Internal references:_

    * [`projects`](../properties/projects.md)

    _External references:_

    * [`count`](../../jbprojects/properties/count.md)
3.  Store the provided controller as the controller of the project.

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
  - the message sender is the project's current controller. 
  - or, an allowedlisted address is setting a controller for a project that doesn't already have a controller.

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
    (msg.sender == address(controllerOf[_projectId]) ||
      (isAllowedToSetFirstController[msg.sender] &&
        controllerOf[_projectId] == IJBController(address(0))))
  )
{
  // The project must exist.
  if (projects.count() < _projectId) revert INVALID_PROJECT_ID_IN_DIRECTORY();

  // Set the new controller.
  controllerOf[_projectId] = _controller;

  emit SetController(_projectId, _controller, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                            | Description                                                      |
| --------------------------------- | ---------------------------------------------------------------- |
| **`INVALID_PROJECT_ID`**          | Thrown if the provided project doesn't yet exist.                |
{% endtab %}

{% tab title="Events" %}
| Name                                              | Data                                                                                                                                                                                                             |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetController`**](../events/setcontroller.md)           | <ul><li><code>int256 indexed projectId</code></li><li><code>[`IJBController`](../../../interfaces/ijbcontroller.md)indexed controller</code></li><li><code>address caller</code></li></ul>                                       |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
