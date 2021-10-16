# setControllerOf

Contract:[`JBDirectory`](../)​‌

Interface: `IJBDirectory`

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Update the controller that manages how terminals interact with tokens and funding cycles.

  @dev 
  A controller cant be set if:
    - case 1: the project owner or an operator is changing the controller.
    - case 2: the controller hasn't been set yet and the message sender is the controller being set.
    - case 3: the current controller is setting a new controller.

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
    (address(controllerOf[_projectId]) == address(0) && msg.sender == address(_controller)) ||
      address(controllerOf[_projectId]) == msg.sender
  )
{
  // Get a reference to the current controller being used.
  IJBController _currentController = controllerOf[_projectId];

  // If the controller is already set, nothing to do.
  if (_currentController == _controller) return;

  // The project must exist.
  require(projects.count() >= _projectId, 'NOT_FOUND');

  // Can't set the zero address.
  require(_controller != IJBController(address(0)), 'ZERO_ADDRESS');

  // Set the new controller.
  controllerOf[_projectId] = _controller;

  emit SetController(_projectId, _controller, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                         | Description                                              |
| ------------------------------ | -------------------------------------------------------- |
| **`0x2b: NOT_FOUND`** | Thrown if the provided project doesn't yet exist. |
| **`0x2c: ZERO_ADDRESS`** | Thrown if the provided controller is the zero address. |
{% endtab %}

{% tab title="Events" %}
| Name                            | Data                                                                                                                                                                                                                                                                          |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetController`**](../events/burn.md) | <ul><li><code>int256 indexed projectId</code></li><li><a href="../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>indexed controller</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}
