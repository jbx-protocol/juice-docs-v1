# Read

### canPrintPreminedTickets

Whether or not a project can still print premined tickets.

**Params:**

*  \_projectId The ID of the project to get the status of.

**@return** Boolean flag.

```lua

function canPrintPreminedTickets(uint256 _projectId)
    public
    view
    override
    returns (bool)
{
    return
        // The total supply of tickets must equal the preconfigured ticket count.
        ticketBooth.totalSupplyOf(_projectId) ==
        _preconfigureTicketCountOf[_projectId] &&
        // The above condition is still possible after post-configured tickets have been printed due to ticket redeeming.
        // The only case when processedTicketTracker is 0 is before redeeming and printing reserved tickets.
        _processedTicketTrackerOf[_projectId] >= 0 &&
        uint256(_processedTicketTrackerOf[_projectId]) ==
        _preconfigureTicketCountOf[_projectId];
}
```



### claimableOverflowOf

The amount of tokens that can be claimed by the given address.

The \_account must have at least \_count tickets for the specified project.   
If there is a funding cycle reconfiguration ballot open for the project, the project's current bonding curve        is bypassed.

**Params:**

*  \_account The address to get an amount for.  
* \_projectId The ID of the project to get a claimable amount for.  
* \_count The number of Tickets that would be redeemed to get the resulting amount.

  **@return** amount The amount of tokens that can be claimed

```lua
function claimableOverflowOf(
    address _account,
    uint256 _projectId,
    uint256 _count
) public view override returns (uint256) {
    // The holder must have the specified number of the project's tickets.
    require(
        ticketBooth.balanceOf(_account, _projectId) >= _count,
        "TerminalV1::claimableOverflow: INSUFFICIENT_TICKETS"
    );

    // Get a reference to the current funding cycle for the project.
    FundingCycle memory _fundingCycle = fundingCycles.currentOf(_projectId);

    // There's no overflow if there's no funding cycle.
    if (_fundingCycle.id == 0) return 0;

    // Get the amount of current overflow.
    uint256 _currentOverflow = _overflowFrom(_fundingCycle);

    // If there is no overflow, nothing is claimable.
    if (_currentOverflow == 0) return 0;

    // Get the total number of tickets in circulation.
    uint256 _totalSupply = ticketBooth.totalSupplyOf(_projectId);

    // Get the number of reserved tickets the project has.
    // The reserved rate is in bits 8-15 of the metadata.
    uint256 _reservedTicketAmount = _reservedTicketAmountFrom(
        _processedTicketTrackerOf[_projectId],
        uint256(uint8(_fundingCycle.metadata >> 8)),
        _totalSupply
    );

    // If there are reserved tickets, add them to the total supply.
    if (_reservedTicketAmount > 0)
        _totalSupply = _totalSupply + _reservedTicketAmount;

    // If the amount being redeemed is the the total supply, return the rest of the overflow.
    if (_count == _totalSupply) return _currentOverflow;

    // Get a reference to the linear proportion.
    uint256 _base = PRBMath.mulDiv(_currentOverflow, _count, _totalSupply);

    // Use the reconfiguration bonding curve if the queued cycle is pending approval according to the previous funding cycle's ballot.
    uint256 _bondingCurveRate = fundingCycles.currentBallotStateOf(
        _projectId
    ) == BallotState.Active // The reconfiguration bonding curve rate is stored in bytes 24-31 of the metadata property.
        ? uint256(uint8(_fundingCycle.metadata >> 24)) // The bonding curve rate is stored in bytes 16-23 of the data property after.
        : uint256(uint8(_fundingCycle.metadata >> 16));

    // The bonding curve formula.
    // https://www.desmos.com/calculator/sp9ru6zbpk
    // where x is _count, o is _currentOverflow, s is _totalSupply, and r is _bondingCurveRate.

    // These conditions are all part of the same curve. Edge conditions are separated because fewer operation are necessary.
    if (_bondingCurveRate == 200) return _base;
    if (_bondingCurveRate == 0)
        return PRBMath.mulDiv(_base, _count, _totalSupply);
    return
        PRBMath.mulDiv(
            _base,
            _bondingCurveRate +
                PRBMath.mulDiv(
                    _count,
                    200 - _bondingCurveRate,
                    _totalSupply
                ),
            200
        );
}

```

### currentOverflowOf

Gets the current overflowed amount for a specified project.   

**Params:**

*  \_projectId The ID of the project to get overflow for.  

**@return** overflow The current overflow of funds for the project.

```lua

function currentOverflowOf(uint256 _projectId)
    external
    view
    override
    returns (uint256 overflow)
{
    // Get a reference to the project's current funding cycle.
    FundingCycle memory _fundingCycle = fundingCycles.currentOf(_projectId);

    // There's no overflow if there's no funding cycle.
    if (_fundingCycle.id == 0) return 0;

    return _overflowFrom(_fundingCycle);
}

```

### reservedTicketBalanceOf

Gets the amount of reserved tickets that a project has.

  **Params:**

* \_projectId The ID of the project to get overflow for.
* \_reservedRate The reserved rate to use to make the calculation.  

**@return** amount overflow The current overflow of funds for the project.

```lua

function reservedTicketBalanceOf(uint256 _projectId, uint256 _reservedRate)
    external
    view
    override
    returns (uint256)
{
    return
        _reservedTicketAmountFrom(
            _processedTicketTrackerOf[_projectId],
            _reservedRate,
            ticketBooth.totalSupplyOf(_projectId)
        );
}
```

### 

### TO-DO

Read functions yet to be documented

```text
function governance() external view returns (address payable);

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

