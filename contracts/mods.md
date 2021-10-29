# Mods

@notice Stores mods for each project.

@dev Mods can be used to distribute a percentage of payments or tickets to preconfigured beneficiaries.

## `payoutModsOf(uint256 _projectId, uint256 _configuration) → struct PayoutMod[]` (external)

```
  Get all payout mods for the specified project ID.

  @param _projectId The ID of the project to get mods for.
  @param _configuration The configuration to get mods for.

  @return An array of all mods for the project.
```

## `ticketModsOf(uint256 _projectId, uint256 _configuration) → struct TicketMod[]` (external)

```
  Get all ticket mods for the specified project ID.

  @param _projectId The ID of the project to get mods for.
  @param _configuration The configuration to get mods for.

  @return An array of all mods for the project.
```

## `constructor(contract IProjects _projects, contract IOperatorStore _operatorStore, contract ITerminalDirectory _terminalDirectory)` (public)

## `setPayoutMods(uint256 _projectId, uint256 _configuration, struct PayoutMod[] _mods)` (external)

```
  Adds a mod to the payout mods list.

  @dev
  Only the owner or operator of a project can make this call, or the current terminal of the project.

  @param _projectId The project to add a mod to.
  @param _configuration The configuration to set the mods to be active during.
  @param _mods The payout mods to set.
```

## `setTicketMods(uint256 _projectId, uint256 _configuration, struct TicketMod[] _mods)` (external)

```
  Adds a mod to the ticket mods list.

  @dev
  Only the owner or operator of a project can make this call, or the current terminal of the project.

  @param _projectId The project to add a mod to.
  @param _configuration The configuration to set the mods to be active during.
  @param _mods The ticket mods to set.
```
