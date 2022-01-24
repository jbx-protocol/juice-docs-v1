# migrate

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../interfaces/ijbethpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to migrate its funds and operations to a new terminal.**

_Only a project's owner or a designated operator can migrate it._

## Definition

```solidity
function migrate(uint256 _projectId, IJBTerminal _to)
  external
  override
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_TERMINAL) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being migrated.
  * `_to` is the terminal contract that will gain the project's funds.
* Through the [`requirePermission`](../../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.MIGRATE_TERMINAL` permission by the project owner for the provided `_projectId`.
* The function cannot be accessed recursively or while other `nonReentrant` functions in this contract are in progress.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
* The function doesn't return anything.

## Body

1.  Make sure the token type of the terminal being migrated to matches the token type of this terminal.

    ```solidity
    // The terminal being migrated to must accept the same token as this terminal.
    if (token != _to.token()) {
      revert TERMINAL_TOKENS_INCOMPATIBLE();
    }
    ```

    _Internal references:_

    * [`token`](../properties/token.md)
2.  Record the migration and get a reference to the project's balance.

    ```solidity
    // Record the migration in the store.
    uint256 _balance = store.recordMigration(_projectId);
    ```

    _External references:_

    * [`recordMigration`](../../jbethpaymentterminalstore/write/recordmigration.md)
3.  If there's a balance to migrate, move the funds over to the new terminal.

    ```solidity
    if (_balance > 0)
      // Withdraw the balance to transfer to the new terminal;
      _to.addToBalanceOf{value: _balance}(_projectId, '');
    ```

    _Internal references:_

    * [`addToBalanceOf`](addtobalanceof.md)
4.  Emit a `Migrate` event with the relevant parameters.

    ```solidity
    emit Migrate(_projectId, _to, _balance, msg.sender);
    ```

    _Event references:_

    * [`Migrate`](../events/migrate.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Allows a project owner to migrate its funds and operations to a new terminal.

  @dev
  Only a project's owner or a designated operator can migrate it.

  @param _projectId The ID of the project being migrated.
  @param _to The terminal contract that will gain the project's funds.
*/
function migrate(uint256 _projectId, IJBTerminal _to)
  external
  override
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.MIGRATE_TERMINAL)
{
  // The terminal being migrated to must accept the same token as this terminal.
  if (token != _to.token()) {
    revert TERMINAL_TOKENS_INCOMPATIBLE();
  }

  // Record the migration in the store.
  uint256 _balance = store.recordMigration(_projectId);

  if (_balance > 0)
    // Withdraw the balance to transfer to the new terminal;
    _to.addToBalanceOf{value: _balance}(_projectId, '');

  emit Migrate(_projectId, _to, _balance, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                                                           |
| ------------------------ | ------------------------------------------------------------------------------------- |
| **`TERMINAL_TOKENS_INCOMPATIBLE`** | Thrown if the terminal being migrated to doesn't use the same token as this terminal. |
{% endtab %}

{% tab title="Events" %}
| Name                                       | Data                                                                                                                                                                                                                                               |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Migrate`**](../events/addtobalance.md) | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
