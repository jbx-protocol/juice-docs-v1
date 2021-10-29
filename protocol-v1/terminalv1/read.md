# Read

### canPrintPreminedTickets

Whether or not a project can still print premined tickets.

**Params:**

* **\_projectId**:The ID of the project to get the status of.

**Returns:** Boolean flag.

```lua
function canPrintPreminedTickets(uint256 _projectId)
    external
    view
    returns (bool);
```

### claimableOverflowOf

The amount of tokens that can be claimed by the given address.

The \_account must have at least \_count tickets for the specified project.\
If there is a funding cycle reconfiguration ballot open for the project, the project's current bonding curve is bypassed.

**Params:**

* **\_account**: The address to get an amount for.
* **\_projectId**: The ID of the project to get a claimable amount for.
*   **\_count**: The number of Tickets that would be redeemed to get the resulting amount.

    **Returns**: The amount of tokens that can be claimed

```lua
function claimableOverflowOf(
    address _account,
    uint256 _projectId,
    uint256 _count
) external view returns (uint256)
```

### currentOverflowOf

Gets the current overflowed amount for a specified project.

**Params:**

* **\_projectId**: The ID of the project to get overflow for.

**Returns** The current overflow of funds for the project.

```lua
function currentOverflowOf(uint256 _projectId)
    external
    view
    returns (uint256 overflow)
```

### governance

**Returns** The address of the current governance

```
function governance() external view returns (address payable);
```

### reservedTicketBalanceOf

Gets the amount of reserved tickets that a project has.

**Params:**

* **\_projectId**: The ID of the project to get overflow for.
* **\_reservedRate**: The reserved rate to use to make the calculation.

**Returns:** amount overflow The current overflow of funds for the project.

```lua
function reservedTicketBalanceOf(uint256 _projectId, uint256 _reservedRate)
    external
    view
    returns (uint256);
```

###

### TO-DO

Read functions yet to be documented

```

function pendingGovernance() external view returns (address payable);

function projects() external view returns (IProjects);

function fundingCycles() external view returns (IFundingCycles);

function ticketBooth() external view returns (ITicketBooth);

function prices() external view returns (IPrices);

function modStore() external view returns (IModStore);

function balanceOf(uint256 _projectId) external view returns (uint256);

function fee() external view returns (uint256);

function terminalDirectory() external view returns (ITerminalDirectory);

function migrationIsAllowed(ITerminal _terminal)
    external
    view
    returns (bool);
```
