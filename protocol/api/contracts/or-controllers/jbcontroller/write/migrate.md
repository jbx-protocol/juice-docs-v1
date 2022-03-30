# migrate

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to migrate from this controller to another.**

_Only a project's owner or a designated operator can migrate it._

### Definition

```solidity
function migrate(uint256 _projectId, IJBController _to)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_CONTROLLER) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project that will be migrated from this controller.
  * `_to` is the controller to which the project is migrating.
* Through the [`requirePermission`](../../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the [`JBOperations.MIGRATE_CONTROLLER`](../../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`.
* The function doesn't return anything.

### Body

1.  Make sure this controller is the project's current controller. 

    ```solidity
    // This controller must be the project's current controller.
    if (directory.controllerOf(_projectId) != this) revert NOT_CURRENT_CONTROLLER();
    ```

    _External references:_

    * [`controllerOf`](../../../jbdirectory/properties/controllerof.md)
2.  Get a reference to the current funding cycle for the project.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
3.  Make sure the project's current funding cycle is configured to allow controller migrations.

    ```solidity
    // Migration must be allowed.
    if (!_fundingCycle.controllerMigrationAllowed()) revert MIGRATION_NOT_ALLOWED();
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.controllerMigrationAllowed(...)`
4.  Distribute any outstanding reserved tokens. There are reserved tokens to be distributed if the tracker does not equal the token's total supply.

    ```solidity
    // All reserved tokens must be minted before migrating.
    if (uint256(_processedTokenTrackerOf[_projectId]) != tokenStore.totalSupplyOf(_projectId))
      _distributeReservedTokensOf(_projectId, '');
    ```

    _Internal references:_

    * [`_processedTokenTrackerOf`](../properties/_processedtokentrackerof.md)
    * [`_distributeReservedTokensOf`](_distributereservedtokensof.md)

    _External references:_

    * [`totalSupplyOf`](../../../jbtokenstore/read/totalsupplyof.md)
5.  Let the new controller know that a migration to it is happening.

    ```solidity
    // Make sure the new controller is prepped for the migration.
    _to.prepForMigrationOf(_projectId, this);
    ```

    _External references:_

    * [`prepForMigrationOf`](prepformigrationof.md)
6.  Set the new controller of the project.

    ```solidity
    // Set the new controller.
    directory.setControllerOf(_projectId, _to);
    ```

    _External references:_

    * [`setControllerOf`](../../../jbdirectory/write/setcontrollerof.md)
7.  Emit a `Migrate` event with the relevant parameters.

    ```solidity
    emit Migrate(_projectId, _to, msg.sender);
    ```

    _Event references:_

    * [`Migrate`](../events/migrate.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Allows a project to migrate from this controller to another.

  @dev
  Only a project's owner or a designated operator can migrate it.

  @param _projectId The ID of the project that will be migrated from this controller.
  @param _to The controller to which the project is migrating.
*/
function migrate(uint256 _projectId, IJBController _to)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_CONTROLLER)
{
  // This controller must be the project's current controller.
  if (directory.controllerOf(_projectId) != this) revert NOT_CURRENT_CONTROLLER();

  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Migration must be allowed.
  if (!_fundingCycle.controllerMigrationAllowed()) revert MIGRATION_NOT_ALLOWED();

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
| String                              | Description                                                                         |
| ----------------------------------- | ----------------------------------------------------------------------------------- |
| **`NOT_CURRENT_CONTROLLER`** | Thrown if the controller isn't the project's current controller.                    |
| **`MIGRATION_NOT_ALLOWED`**         | Thrown if the project's current funding cycle doesn't allow a controller migration. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                                | Data                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Migrate`**](../events/migrate.md)                                               | <ul><li><code>uint256 projectId</code></li><li><code>[`IJBController`](../../../../interfaces/ijbcontroller.md)to</code></li><li><code>address caller</code></li></ul>                                                                                                                  |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
