# prepForMigrationOf

Contract:[`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows other controllers to signal to this one that a migration is expected for the specified project.**

# Definition

```solidity
function prepForMigrationOf(uint256 _projectId, IJBController) external override { ... }
```

* Arguments:
  * `_projectId` is the ID of the project that will be migrated to this controller.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function doesn't return anything.

# Body

1.  Make sure this controller isn't the project's current controller. If it is, there shouldn't be a need to prepare anything.

    ```solidity
    // This controller must not be the project's current controller.
    require(directory.controllerOf(_projectId) != this, '0x34: NO_OP');
    ```

    _External references:_

    * [`controllerOf`](../../../jbdirectory/read/controllerof.md)
2.  Update the processed token tracker to equal the current total supply of tokens. This prevents any inadvertant outstanding reserved tokens from being distributable upon migrating to this controller.

    ```solidity
    // Set the tracker as the total supply.
    _processedTokenTrackerOf[_projectId] = int256(tokenStore.totalSupplyOf(_projectId));
    ```

    _Internal references:_

    * [`_processedTokenTrackerOf`](../properties/\_processedtokentrackerof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Allows other controllers to signal to this one that a migration is expected for the specified project.

  @param _projectId The ID of the project that will be migrated to this controller.
*/
function prepForMigrationOf(uint256 _projectId, IJBController) external override {
  // This controller must not be the project's current controller.
  require(directory.controllerOf(_projectId) != this, '0x34: NO_OP');

  // Set the tracker as the total supply.
  _processedTokenTrackerOf[_projectId] = int256(tokenStore.totalSupplyOf(_projectId));
}
```
{% endtab %}

{% tab title="Errors" %}
| String            | Description                                                         |
| ----------------- | ------------------------------------------------------------------- |
| **`0x34: NO_OP`** | Thrown if the controller is the current controller for the project. |
{% endtab %}

{% tab title="Events" %}
| Name                                    | Data                                                                                                                                                                                                                 |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetSplit`**](../events/setsplit.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><code>Split split</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
