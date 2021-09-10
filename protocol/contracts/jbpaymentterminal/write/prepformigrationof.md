# prepForMigrationOf

```javascript
/**
  @notice
  Sets up any peice of internal state necessary for the specified project to migrate to this terminal.

  @dev
  This must be called before this contract is the current terminal for the project.

  @dev
  This function can be called many times, but must be called in the same transaction that migrates a project to this terminal.

  @param _projectId The ID of the project that is being migrated to this terminal.
*/
function prepForMigrationOf(uint256 _projectId)
    external
    override
    nonReentrant
{
    // This function can only be called if this contract isn't already the project's current terminal.
    require(
        directory.terminalOf(_projectId) != this,
        "JBPaymentTerminal: NOT_ALLOWED"
    );
    data.recordPrepForMigrationOf(_projectId);
}
```

