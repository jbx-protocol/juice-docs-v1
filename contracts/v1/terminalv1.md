---
description: 'Interfaces the contracts and the user, working as a top-level contract.'
---

# TerminalV1

## Constructor

**Params:**

*   projects A Projects contract which mints ERC-721's that represent project ownership and transfers**.** 
*   \_fundingCycles A funding cycle configuration store.
*   A contract that manages Ticket printing and redeeming.
*   \_modStore A storage for a project's mods.
*   \_prices A price feed contract to use.
*   \_terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.

\*/

```text

constructor(
    IProjects _projects,
    IFundingCycles _fundingCycles,
    ITicketBooth _ticketBooth,
    IOperatorStore _operatorStore,
    IModStore _modStore,
    IPrices _prices,
    ITerminalDirectory _terminalDirectory,
    address payable _governance
) Operatable(_operatorStore) {
    require(
        _projects != IProjects(address(0)) &&
            _fundingCycles != IFundingCycles(address(0)) &&
            _ticketBooth != ITicketBooth(address(0)) &&
            _modStore != IModStore(address(0)) &&
            _prices != IPrices(address(0)) &&
            _terminalDirectory != ITerminalDirectory(address(0)) &&
            _governance != address(address(0)),
        "TerminalV1: ZERO_ADDRESS"
    );
    projects = _projects;
    fundingCycles = _fundingCycles;
    ticketBooth = _ticketBooth;
    modStore = _modStore;
    prices = _prices;
    terminalDirectory = _terminalDirectory;
    governance = _governance;
}
```

## Read

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

## Write



### Deploy

Deploys a project. This will mint an ERC-721 into the \_owner's account, configure a first funding cycle, and set up any mods.

Each operation within this transaction can be done in sequence separately  
Anyone can deploy a project on an owner's behalf.

**Params:**

* \_owner The address that will own the project.
* \_handle The project's unique handle.
* \_uri A link to information about the project and this funding cycle.
* \_properties The funding cycle configuration.
  * \_properties.target The amount that the project wants to receive in this funding cycle. Sent as a wad.
  * \_properties.currency The currency of the \`target\`. Send 0 for ETH or 1 for USD.
  * \_properties.duration The duration of the funding stage for which the \`target\` amount is needed. Measured in days. Send 0 for a boundless cycle reconfigurable at any time.
  * \_properties.cycleLimit The number of cycles that this configuration should last for before going back to the last permanent. This has no effect for a project's first funding cycle.
  *  \_properties.discountRate A number from 0-200 indicating how valuable a contribution to this funding stage is compared to the project's previous funding stage.
    * If it's 200, each funding stage will have equal weight.
    * If the number is 180, a contribution to the next funding stage will only give you 90% of tickets given to a contribution of the same amount during the current funding stage.
    * If the number is 0, an non-recurring funding stage will get made.
  * \_configuration.ballot The new ballot that will be used to approve subsequent reconfigurations.
* \_metadata A struct specifying the TerminalV1 specific params \_bondingCurveRate, and \_reservedRate.
  * \_reservedRate A number from 0-200 indicating the percentage of each contribution's tickets that will be reserved for the project owner.
  * \_bondingCurveRate The rate from 0-200 at which a project's Tickets can be redeemed for surplus.
    * The bonding curve formula can be found [here](https://www.desmos.com/calculator/sp9ru6zbpk).

       where x is \_count, o is \_currentOverflow, s is \_totalSupply, and r is \_bondingCurveRate.
  * \_reconfigurationBondingCurveRate The bonding curve rate to apply when there is an active ballot.
* \_payoutMods Any payout mods to set.
* \_ticketMods Any ticket mods to set.

```lua

function deploy(
    address _owner,
    bytes32 _handle,
    string calldata _uri,
    FundingCycleProperties calldata _properties,
    FundingCycleMetadata calldata _metadata,
    PayoutMod[] memory _payoutMods,
    TicketMod[] memory _ticketMods
) external override {
    // Make sure the metadata checks out. If it does, return a packed version of it.
    uint256 _packedMetadata = _validateAndPackFundingCycleMetadata(
        _metadata
    );

    // Create the project for the owner.
    uint256 _projectId = projects.create(_owner, _handle, _uri, this);

    // Configure the funding stage's state.
    FundingCycle memory _fundingCycle = fundingCycles.configure(
        _projectId,
        _properties,
        _packedMetadata,
        fee,
        true
    );

    // Set payout mods if there are any.
    if (_payoutMods.length > 0)
        modStore.setPayoutMods(
            _projectId,
            _fundingCycle.configured,
            _payoutMods
        );

    // Set ticket mods if there are any.
    if (_ticketMods.length > 0)
        modStore.setTicketMods(
            _projectId,
            _fundingCycle.configured,
            _ticketMods
        );
}
```



### Configure

Configures the properties of the current funding cycle if the project hasn't distributed tickets yet, or sets the properties of the proposed funding cycle that will take effect once the current one expires if it is approved by the current funding cycle's ballot.  

Only a project's owner or a designated operator can configure its funding cycles.

**Params:**

* \_projectId The ID of the project being reconfigured. 
* \_properties The funding cycle configuration.
* \_properties.target The amount that the project wants to receive in this funding stage. Sent as a wad.
  * \_properties.currency The currency of the \`target\`. Send 0 for ETH or 1 for USD.
  * \_properties.duration The duration of the funding stage for which the \`target\` amount is needed. Measured in days. Send 0 for a boundless cycle reconfigurable at any time.
  * \_properties.cycleLimit The number of cycles that this configuration should last for before going back to the last permanent. This has no effect for a project's first funding cycle.
  * \_properties.discountRate A number from 0-200 indicating how valuable a contribution to this funding stage is compared to the project's previous funding stage.
    * If it's 200, each funding stage will have equal weight.
    * If the number is 180, a contribution to the next funding stage will only give you 90% of tickets given to a contribution of the same amount during the current funding stage.
    * If the number is 0, an non-all transactions except the constructor are write functions recurring funding stage will get made.
  * \_properties.ballot The new ballot that will be used to approve subsequent reconfigurations.
*  \_metadata is a struct specifying the TerminalV1 specific params \_bondingCurveRate, and \_reservedRate.

  * * \_reservedRate A number from 0-200 indicating the percentage of each contribution's tickets that will be reserved for the project owner.
    * \_bondingCurveRate The rate from 0-200 at which a project's Tickets can be redeemed for surplus. The bonding curve formula can be found [here](https://www.desmos.com/calculator/sp9ru6zbpk), where x is \_count, o is \_currentOverflow, s is \_totalSupply, and r is \_bondingCurveRate.
    * \_reconfigurationBondingCurveRate The bonding curve rate to apply when there is an active ballot.

 **@return** The ID of the funding cycle that was successfully configured.

```lua
function configure(
    uint256 _projectId,
    FundingCycleProperties calldata _properties,
    FundingCycleMetadata calldata _metadata,
    PayoutMod[] memory _payoutMods,
    TicketMod[] memory _ticketMods
)
    external
    override
    requirePermission(
        projects.ownerOf(_projectId),
        _projectId,
        Operations.Configure
    )
    returns (uint256)
{
    // Make sure the metadata is validated, and pack it into a uint256.
    uint256 _packedMetadata = _validateAndPackFundingCycleMetadata(
        _metadata
    );

    // If the project can still print premined tickets configure the active funding cycle instead of creating a standby one.
    bool _shouldConfigureActive = canPrintPreminedTickets(_projectId);

    // Configure the funding stage's state.
    FundingCycle memory _fundingCycle = fundingCycles.configure(
        _projectId,
        _properties,
        _packedMetadata,
        fee,
        _shouldConfigureActive
    );

    // Set payout mods for the new configuration if there are any.
    if (_payoutMods.length > 0)
        modStore.setPayoutMods(
            _projectId,
            _fundingCycle.configured,
            _payoutMods
        );

    // Set payout mods for the new configuration if there are any.
    if (_ticketMods.length > 0)
        modStore.setTicketMods(
            _projectId,
            _fundingCycle.configured,
            _ticketMods
        );

    return _fundingCycle.id;
}
```

### 

### printPreminedTickets

Allows a project to print tickets for a specified beneficiary before payments have been received.  @dev 

This can only be done if the project hasn't yet received a payment after configuring a funding cycle.  
Only a project's owner or a designated operator can print premined tickets.

*  \_projectId The ID of the project to premine tickets for.
*  \_amount The amount to base the ticket premine off of.
*  \_currency The currency of the amount to base the ticket premine off of. 
*  \_beneficiary The address to send the printed tickets to.
*  \_memo A memo to leave with the printing.
*  \_preferUnstakedTickets If there is a preference to unstake the printed tickets.

```lua
function printPreminedTickets(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    address _beneficiary,
    string memory _memo,
    bool _preferUnstakedTickets
)
    external
    override
    requirePermission(
        projects.ownerOf(_projectId),
        _projectId,
        Operations.PrintPreminedTickets
    )
{
    // Can't send to the zero address.
    require(
        _beneficiary != address(0),
        "TerminalV1::printTickets: ZERO_ADDRESS"
    );

    // Get the current funding cycle to read the weight and currency from.
    uint256 _weight = fundingCycles.BASE_WEIGHT();

    // Get the current funding cycle to read the weight and currency from.
    // Get the currency price of ETH.
    uint256 _ethPrice = prices.getETHPriceFor(_currency);

    // Multiply the amount by the funding cycle's weight to determine the amount of tickets to print.
    uint256 _weightedAmount = PRBMathUD60x18.mul(
        PRBMathUD60x18.div(_amount, _ethPrice),
        _weight
    );

    // Make sure the project hasnt printed tickets that werent preconfigure.
    // Do this check after the external calls above.
    require(
        canPrintPreminedTickets(_projectId),
        "TerminalV1::printTickets: ALREADY_ACTIVE"
    );

    // Set the preconfigure tickets as processed so that reserved tickets cant be minted against them.
    // Make sure int casting isnt overflowing the int. 2^255 - 1 is the largest number that can be stored in an int.
    require(
        _processedTicketTrackerOf[_projectId] < 0 ||
            uint256(_processedTicketTrackerOf[_projectId]) +
                uint256(_weightedAmount) <=
            uint256(type(int256).max),
        "TerminalV1::printTickets: INT_LIMIT_REACHED"
    );

    _processedTicketTrackerOf[_projectId] =
        _processedTi    ​

  @devcketTrackerOf[_projectId] +
        int256(_weightedAmount);

    // Set the count of preconfigure tickets this project has printed.
    _preconfigureTicketCountOf[_projectId] =
        _preconfigureTicketCountOf[_projectId] +
        _weightedAmount;

    // Print the project's tickets for the beneficiary.
    ticketBooth.print(
        _beneficiary,
        _projectId,
        _weightedAmount,
        _preferUnstakedTickets
    );

    emit PrintPreminedTickets(
        _projectId,
        _beneficiary,
        _amount,
        _currency,
        _memo,
        msg.sender
    );
}
```



### pay

Contribute ETH to a project.

Print's the project's tickets proportional to the amount of the contribution.  
The msg.value is the amount of the contribution in wei.

**Params:**

*  \_projectId The ID of the project being contribute to.
*  \_beneficiary The address to print Tickets for.  \_memo A memo that will be included in the published event.
*  \_preferUnstakedTickets Whether ERC20's should be unstaked automatically if they have been issued.

**@return** The ID of the funding cycle that the payment was made during.

```lua

function pay(
    uint256 _projectId,
    address _beneficiary,
    string calldata _memo,
    bool _preferUnstakedTickets
) external payable override returns (uint256) {
    // Positive payments only.
    require(msg.value > 0, "TerminalV1::pay: BAD_AMOUNT");

    // Cant send tickets to the zero address.
    require(_beneficiary != address(0), "TerminalV1::pay: ZERO_ADDRESS");

    return
        _pay(
            _projectId,
            msg.value,
            _beneficiary,
            _memo,
            _preferUnstakedTickets
        );
}
```

### \*\*\*\*

### **tap**

Tap into funds that have been contributed to a project's current funding cycle.

Anyone can tap funds on a project's behalf.

**Params:**

*  \_projectId The ID of the project to which the funding cycle being tapped belongs. 
* \_amount The amount being tapped, in the funding cycle's currency.
*  \_currency The expected currency being tapped. 
* \_minReturnedWei The minimum number of wei that the amount should be valued at.

**@return** The ID of the funding cycle that was tapped.

```lua
function tap(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei
) external override nonReentrant returns (uint256) {
    // Register the funds as tapped. Get the ID of the funding cycle that was tapped.
    FundingCycle memory _fundingCycle = fundingCycles.tap(
        _projectId,
        _amount
    );

    // If there's no funding cycle, there are no funds to tap.
    if (_fundingCycle.id == 0) return 0;

    // Make sure the currency's match.
    require(
        _currency == _fundingCycle.currency,
        "TerminalV1::tap: UNEXPECTED_CURRENCY"
    );

    // Get a reference to this project's current balance, including any earned yield.
    // Get the currency price of ETH.
    uint256 _ethPrice = prices.getETHPriceFor(_fundingCycle.currency);

    // Get the price of ETH.
    // The amount of ETH that is being tapped.
    uint256 _tappedWeiAmount = PRBMathUD60x18.div(_amount, _ethPrice);

    // The amount being tapped must be at least as much as was expected.
    require(
        _minReturnedWei <= _tappedWeiAmount,
        "TerminalV1::tap: INADEQUATE"
    );

    // Get a reference to this project's current balance, including any earned yield.
    uint256 _balance = balanceOf[_fundingCycle.projectId];

    // The amount being tapped must be available.
    require(
        _tappedWeiAmount <= _balance,
        "TerminalV1::tap: INSUFFICIENT_FUNDS"
    );

    // Removed the tapped funds from the project's balance.
    balanceOf[_projectId] = _balance - _tappedWeiAmount;

    // Get a reference to the project owner, which will receive the admin's tickets from paying the fee,
    // and receive any extra tapped funds not allocated to mods.
    address payable _projectOwner = payable(
        projects.ownerOf(_fundingCycle.projectId)
    );

    // Get a reference to the handle of the project paying the fee and sending payouts.
    bytes32 _handle = projects.handleOf(_projectId);

    // Take a fee from the _tappedWeiAmount, if needed.
    // The project's owner will be the beneficiary of the resulting printed tickets from the governance project.
    uint256 _feeAmount = _fundingCycle.fee > 0
        ? _takeFee(
            _tappedWeiAmount,
            _fundingCycle.fee,
            _projectOwner,
            string(bytes.concat("Fee from @", _handle))
        )
        : 0;

    // Payout to mods and get a reference to the leftover transfer amount after all mods have been paid.
    // The net transfer amount is the tapped amount minus the fee.
    uint256 _leftoverTransferAmount = _distributeToPayoutMods(
        _fundingCycle,
        _tappedWeiAmount - _feeAmount,
        string(bytes.concat("Payout from @", _handle))
    );

    // Transfer any remaining balance to the beneficiary.
    if (_leftoverTransferAmount > 0)
        Address.sendValue(_projectOwner, _leftoverTransferAmount);

    emit Tap(
        _fundingCycle.id,
        _fundingCycle.projectId,
        _projectOwner,
        _amount,
        _fundingCycle.currency,
        _tappedWeiAmount - _feeAmount,
        _leftoverTransferAmount,
        _feeAmount,
        msg.sender
    );

    return _fundingCycle.id;
}
```

### 

### redeem

Addresses can redeem their Tickets to claim the project's overflowed ETH. 

Only a ticket's holder or a designated operator can redeem it.

**Params:**

*  \_account The account to redeem tickets for.
*  \_projectId The ID of the project to which the Tickets being redeemed belong. 
* \_count The number of Tickets to redeem.
*  \_minReturnedWei The minimum amount of Wei expected in return.
*  \_beneficiary The address to send the ETH to.
*  \_preferUnstaked If the preference is to redeem tickets that have been converted to ERC-20s.

  **@return** amount The amount of ETH that the tickets were redeemed for.

```text
 
function redeem(
    address _account,
    uint256 _projectId,
    uint256 _count,
    uint256 _minReturnedWei,
    address payable _beneficiary,
    bool _preferUnstaked
)
    external
    override
    nonReentrant
    requirePermissionAllowingWildcardDomain(
        _account,
        _projectId,
        Operations.Redeem
    )
    returns (uint256 amount)
{
    // There must be an amount specified to redeem.
    require(_count > 0, "TerminalV1::redeem: NO_OP");

    // Can't send claimed funds to the zero address.
    require(_beneficiary != address(0), "TerminalV1::redeem: ZERO_ADDRESS");

    // The amount of ETH claimable by the message sender from the specified project by redeeming the specified number of tickets.
    amount = claimableOverflowOf(_account, _projectId, _count);

    // Nothing to do if the amount is 0.
    require(amount > 0, "TerminalV1::redeem: NO_OP");

    // The amount being claimed must be at least as much as was expected.
    require(amount >= _minReturnedWei, "TerminalV1::redeem: INADEQUATE");

    // Remove the redeemed funds from the project's balance.
    balanceOf[_projectId] = balanceOf[_projectId] - amount;

    // Get a reference to the processed ticket tracker for the project.
    int256 _processedTicketTracker = _processedTicketTrackerOf[_projectId];

    // Subtract the count from the processed ticket tracker.
    // Subtract from processed tickets so that the difference between whats been processed and the
    // total supply remains the same.
    // If there are at least as many processed tickets as there are tickets being redeemed,
    // the processed ticket tracker of the project will be positive. Otherwise it will be negative.
    _processedTicketTrackerOf[_projectId] = _processedTicketTracker < 0 // If the tracker is negative, add the count and reverse it.
        ? -int256(uint256(-_processedTicketTracker) + _count) // the tracker is less than the count, subtract it from the count and reverse it.
        : _processedTicketTracker < int256(_count)
        ? -(int256(_count) - _processedTicketTracker) // simply subtract otherwise.
        : _processedTicketTracker - int256(_count);

    // Redeem the tickets, which burns them.
    ticketBooth.redeem(_account, _projectId, _count, _preferUnstaked);

    // Transfer funds to the specified address.
    Address.sendValue(_beneficiary, amount);

    emit Redeem(
        _account,
        _beneficiary,
        _projectId,
        _count,
        amount,
        msg.sender
    );
}
```



### **setFee**

Allow the admin to change the fee. 

Only funding cycle reconfigurations after the new fee is set will use the new fee. All future funding cycles based on configurations made in the past will use the fee that was set at the time of the configuration.  
Only governance can set a new fee.

**Params:**

* \_fee The new fee percent. Out of 200.

```text
function setFee(uint256 _fee) external override onlyGov {
    // Fee must be under 100%.
    require(_fee <= 200, "TerminalV1::setFee: BAD_FEE");

    // Set the fee.
    fee = _fee;

    emit SetFee(_fee);
}
```



### onlyGov

Allows governance to transfer its privileges to another contract.

Only the currency governance can appoint a new governance.

**Params:**

* \_pendingGovernance The governance to transition power to. 
  * This address will have to accept the responsibility in a subsequent transaction.

```text
function appointGovernance(address payable _pendingGovernance)
    external
    override
    onlyGov
{
    // The new governance can't be the zero address.
    require(
        _pendingGovernance != address(0),
        "TerminalV1::appointGovernance: ZERO_ADDRESS"
    );
    // The new governance can't be the same as the current governance.
    require(
        _pendingGovernance != governance,
        "TerminalV1::appointGovernance: NO_OP"
    );

    // Set the appointed governance as pending.
    pendingGovernance = _pendingGovernance;

    emit AppointGovernance(_pendingGovernance);
}
```

### acceptGovernance

Allows contract to accept its appointment as the new governance.

```text
function acceptGovernance() external override {
    // Only the pending governance address can accept.
    require(
        msg.sender == pendingGovernance,
        "TerminalV1::acceptGovernance: UNAUTHORIZED"
    );

    // Get a reference to the pending governance.
    address payable _pendingGovernance = pendingGovernance;

    // Set the govenance to the pending value.
    governance = _pendingGovernance;

    emit AcceptGovernance(_pendingGovernance);
}
```



## Events



