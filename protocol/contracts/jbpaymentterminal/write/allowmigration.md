# allowMigration

```javascript
/**
  @notice
  Adds a Terminal address to the allow list that project owners can migrate its funds and treasury operations to.

  @dev
  Only this contract's owner can add a contract to the migration allow list.

  @param _terminal The terminal contract to allow.
*/
function allowMigration(IJBTerminal _terminal) external override onlyOwner {
    // Toggle the contract as allowed.
    migrationIsAllowed[_terminal] = !migrationIsAllowed[_terminal];

    emit AllowMigration(_terminal);
}
```

