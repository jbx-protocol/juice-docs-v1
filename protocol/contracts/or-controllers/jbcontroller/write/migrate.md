# migrate

Contract:[`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to migrate from this controller to another.**

_Only a project's owner or a designated operator can migrate it._

## Definition

```solidity
function migrate(uint256 _projectId, IJBController _to)
    external
    requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_CONTROLLER)
    nonReentrant { ... }
```

* Arguments:
  * `_projectId` is the ID of the project that will be migrated from this controller..
  * `_to` is the controller to which the project is migrating.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.MIGRATE_CONTROLLER` permission by the project owner for the provided `_projectId`.
* The function doesn't return anything.

## Body

TODO
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Allows a project to migrate from this controller to another.

  @param _projectId The ID of the project that will be migrated from this controller.
  @param _to The controller to which the project is migrating.
*/
function migrate(uint256 _projectId, IJBController _to)
    external
    requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_CONTROLLER)
    nonReentrant
  {
    // This controller must be the project's current controller.
    require(directory.controllerOf(_projectId) == this, 'UNAUTHORIZED');

    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

    // Migration must be allowed
    require(_fundingCycle.controllerMigrationAllowed(), 'TODO');

    // All reserved tokens must be minted before migrating.
    if (uint256(_processedTokenTrackerOf[_projectId]) != tokenStore.totalSupplyOf(_projectId))
      _distributeReservedTokensOf(_projectId, '');

    // Make sure the new controller is prepped for the migration.
    _to.prepForMigrationOf(_projectId, this);

    // Set the new controller.
    directory.setControllerOf(_projectId, _to);

    emit Migrate(_projectId, _to, msg.sender);
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
