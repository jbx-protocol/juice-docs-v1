# ModStore

### Constructor

```javascript
/** 
  @param _projects The contract storing project information
  @param _operatorStore A contract storing operator assignments.
  @param _terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.
*/
constructor(
    IProjects _projects,
    IOperatorStore _operatorStore,
    ITerminalDirectory _terminalDirectory
) Operatable(_operatorStore) TerminalUtility(_terminalDirectory)
```

### Read

```javascript
/**
  @notice 
  Get all payout mods for the specified project ID.

  @param _projectId The ID of the project to get mods for.
  @param _configuration The configuration to get mods for.

  @return An array of all mods for the project.
 */
function payoutModsOf(uint256 _projectId, uint256 _configuration)
    external
    view
    override
    returns (PayoutMod[] memory)
```

```javascript
/**
  @notice 
  Get all ticket mods for the specified project ID.

  @param _projectId The ID of the project to get mods for.
  @param _configuration The configuration to get mods for.

  @return An array of all mods for the project.
 */
function ticketModsOf(uint256 _projectId, uint256 _configuration)
    external
    view
    override
    returns (TicketMod[] memory)
```

### Write

```javascript
/** 
  @notice 
  Adds a mod to the payout mods list.

  @dev
  Only the owner or operator of a project can make this call, or the current terminal of the project.

  @param _projectId The project to add a mod to.
  @param _configuration The configuration to set the mods to be active during.
  @param _mods The payout mods to set.
*/
function setPayoutMods(
    uint256 _projectId,
    uint256 _configuration,
    PayoutMod[] memory _mods
)
    external
    override
    requirePermissionAcceptingAlternateAddress(
        projects.ownerOf(_projectId),
        _projectId,
        Operations.SetPayoutMods,
        address(terminalDirectory.terminalOf(_projectId))
    )
```

```javascript
/** 
  @notice 
  Adds a mod to the ticket mods list.

  @dev
  Only the owner or operator of a project can make this call, or the current terminal of the project.

  @param _projectId The project to add a mod to.
  @param _configuration The configuration to set the mods to be active during.
  @param _mods The ticket mods to set.
*/
function setTicketMods(
    uint256 _projectId,
    uint256 _configuration,
    TicketMod[] memory _mods
)
    external
    override
    requirePermissionAcceptingAlternateAddress(
        projects.ownerOf(_projectId),
        _projectId,
        Operations.SetTicketMods,
        address(terminalDirectory.terminalOf(_projectId))
    )
```

