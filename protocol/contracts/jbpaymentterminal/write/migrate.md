# migrate

```javascript
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
    requirePermission(
        projects.ownerOf(_projectId),
        _projectId,
        Operations.Migrate
    )
{
    // The data layer must be the project's current terminal.
    require(
        address(directory.terminalOf(_projectId)) == address(data),
        "JBPaymentTerminal: UNAUTHORIZED"
    );

    // The migration destination must be allowed.
    require(migrationIsAllowed[_to], "JBPaymentTerminal: NOT_ALLOWED");

    // Allow the terminal receiving the project's funds and operations to prepare for the migration.
    _to.prepForMigrationOf(_projectId);

    // Record the migration in the data layer.
    uint256 _balance = data.recordMigration(_projectId);

    // Switch the terminal that the terminal directory will point to for this project.
    directory.setTerminal(_projectId, _to);

    // Move the funds to the new contract if needed.
    if (_balance > 0)
        _to.addToBalance{value: _balance}(
            _projectId,
            "Migration from JBPaymentTerminal"
        );

    emit Migrate(_projectId, _to, _balance, msg.sender);
}
```

