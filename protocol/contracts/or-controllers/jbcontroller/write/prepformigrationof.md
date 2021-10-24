# prepForMigrationOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

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
* The function overrides a function definition from the `IJBController` interface.
* The function doesn't return anything.

# Body
TODO
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
  require(directory.controllerOf(_projectId) != this, 'UNAUTHORIZED');

  // Set the tracker as the total supply.
  _processedTokenTrackerOf[_projectId] = int256(tokenStore.totalSupplyOf(_projectId));
}
```
{% endtab %}

{% tab title="Errors" %}
| String                  | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| **`0x0f: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
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
