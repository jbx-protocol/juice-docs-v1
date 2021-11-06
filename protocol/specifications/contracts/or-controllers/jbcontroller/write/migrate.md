# migrate

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to migrate from this controller to another.**

_Only a project's owner or a designated operator can migrate it._

# Definition

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
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The function doesn't return anything.

# Body

1.  Make sure this controller is the project's current controller. Migrating away from a controller that isn't the project's current one wouldn't do anything.

    ```solidity
    // This controller must be the project's current controller.
    require(directory.controllerOf(_projectId) == this, '0x35: NO_OP');
    ```

    _External references:_

    * [`controllerOf`](../../../jbdirectory/read/controllerof.md)
2.  Get a reference to the current funding cycle for the project.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
3.  Make sure the project's current funding cycle is configured to allow controller migrations.

    ```solidity
    // Migration must be allowed
    require(_fundingCycle.controllerMigrationAllowed(), '0x36: NOT_ALLOWED');
    ```
4.  Distribute any outstanding reserved tokens. There are reserved tokens to be distributed if the tracker does not equal the token's total supply.

    ```solidity
    // All reserved tokens must be minted before migrating.
    if (uint256(_processedTokenTrackerOf[_projectId]) != tokenStore.totalSupplyOf(_projectId))
      _distributeReservedTokensOf(_projectId, '');
    ```

    _Internal references:_

    * [`_processedTokenTrackerOf`](../read/\_processedtokentrackerof.md)
    * [`_distributeReservedTokensOf`](\_distributereservedtokensof.md)

    _External references:_

    * [`totalSupplyOf`](../../../jbtokenstore/read/totalsupplyof.md)
5.  Let the new controller know that a migration to it is happening.

    ```solidity
    // Make sure the new controller is prepped for the migration.
    _to.prepForMigrationOf(_projectId, this);
    ```

    _Internal references:_

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

  @param _projectId The ID of the project that will be migrated from this controller.
  @param _to The controller to which the project is migrating.
*/
function migrate(uint256 _projectId, IJBController _to)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_CONTROLLER)
  nonReentrant
{
  // This controller must be the project's current controller.
  require(directory.controllerOf(_projectId) == this, '0x35: NO_OP');

  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Migration must be allowed
  require(_fundingCycle.controllerMigrationAllowed(), '0x36: NOT_ALLOWED');

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
| String                  | Description                                                                         |
| ----------------------- | ----------------------------------------------------------------------------------- |
| **`0x35: NO_OP`**       | Thrown if the controller isn't the project's current controller.                    |
| **`0x36: NOT_ALLOWED`** | Thrown if the project's current funding cycle doesn't allow a controller migration. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                                | Data                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Migrate`**](../events/migrate.md)                                               | <ul><li><code>uint256 projectId</code></li><li><a href="../../../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>to</code></li><li><code>address caller</code></li></ul>                                                                                                                                 |
| [**`DistributeReservedTokens`**](../events/distributereservedtokens.md)             | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed beneficiary</code></li><li><code>uint256 count</code></li><li><code>uint256 projectOwnerTokenCount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
| [**`DistributeToReservedTokenSplit`**](../events/distributetoreservedtokensplit.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><a href="../../../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>split</code></li><li><code>uint256 tokenCount</code></li><li><code>address caller</code></li></ul>                                 |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
