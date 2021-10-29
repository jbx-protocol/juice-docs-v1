---
description: >-
  This contract manages the Juicebox ecosystem, serves as a payment terminal,
  and custodies all funds.
---

# TerminalV1

### Events

```javascript
event Configure(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address caller
)
```

```
event Tap(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address indexed beneficiary,
    uint256 amount,
    uint256 currency,
    uint256 netTransferAmount,
    uint256 beneficiaryTransferAmount,
    uint256 govFeeAmount,
    address caller
)
```

```javascript
event Redeem(
    address indexed holder,
    address indexed beneficiary,
    uint256 indexed _projectId,
    uint256 amount,
    uint256 returnAmount,
    address caller
)
```

```
event PrintReserveTickets(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address indexed beneficiary,
    uint256 count,
    uint256 beneficiaryTicketAmount,
    address caller
)
```

```
event DistributeToPayoutMod(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    PayoutMod mod,
    uint256 modCut,
    address caller
)
```

```
event DistributeToTicketMod(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    TicketMod mod,
    uint256 modCut,
    address caller
)
```

```
event AppointGovernance(address governance)
```

```
event AcceptGovernance(address governance)
```

```
event PrintPreminedTickets(
    uint256 indexed projectId,
    address indexed beneficiary,
    uint256 amount,
    uint256 currency,
    string memo,
    address caller
)
```

```javascript
event Deposit(uint256 amount)
```

```javascript
event SetFee(uint256 _amount)
```

```javascript
event Pay(
    uint256 indexed fundingCycleId,
    uint256 indexed projectId,
    address indexed beneficiary,
    uint256 amount,
    string note,
    address caller
)
```

```javascript
event AddToBalance(
    uint256 indexed projectId,
    uint256 value,
    address caller
)
```

```javascript
event AllowMigration(ITerminal allowed)
```

```javascript
event Migrate(
    uint256 indexed projectId,
    ITerminal indexed to,
    uint256 _amount,
    address caller
)
```

### Constructor

```
/** 
  @param _projects A Projects contract which mints ERC-721's that represent project ownership and transfers.
  @param _fundingCycles A funding cycle configuration store.
  @param _ticketBooth A contract that manages Ticket printing and redeeming.
  @param _modStore A storage for a project's mods.
  @param _prices A price feed contract to use.
  @param _terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.
*/
constructor(
    IProjects _projects,
    IFundingCycles _fundingCycles,
    ITicketBooth _ticketBooth,
    IOperatorStore _operatorStore,
    IModStore _modStore,
    IPrices _prices,
    ITerminalDirectory _terminalDirectory,
    address payable _governance
) Operatable(_operatorStore)
```

### Read

```javascript
/// @notice The governance of the contract who makes fees and can allow new TerminalV1 contracts to be migrated to by project owners.
function governance() external view returns (address payable)
```

```javascript
/// @notice The governance of the contract who makes fees and can allow new TerminalV1 contracts to be migrated to by project owners.
function pendingGovernance() external view returns (address payable)
```

```
function projects() external view returns (IProjects)
```

```
function fundingCycles() external view returns (IFundingCycles)
```

```
function ticketBooth() external view returns (ITicketBooth)
```

```
function prices() external view returns (IPrices)
```

```
function modStore() external view returns (IModStore)
```

```javascript
/// @notice The amount of ETH that each project has.
function balanceOf(uint256 _projectId) external view returns (uint256)
```

```javascript
/// @notice The percent fee the Juicebox project takes from tapped amounts. Out of 200.
function fee() external view returns (uint256)
```

```javascript
/// @notice Whether or not a particular contract is available for projects to migrate their funds and Tickets to.
function migrationIsAllowed(ITerminal _terminal)
    external
    view
    returns (bool);
```

```javascript
   /** 
      @notice 
      Gets the current overflowed amount for a specified project.

      @param _projectId The ID of the project to get overflow for.

      @return overflow The current overflow of funds for the project.
    */
    function currentOverflowOf(uint256 _projectId)
        external
        view
        override
        returns (uint256 overflow)
```

```javascript
/** 
  @notice 
  Gets the amount of reserved tickets that a project has.

  @param _projectId The ID of the project to get overflow for.
  @param _reservedRate The reserved rate to use to make the calculation.

  @return amount overflow The current overflow of funds for the project.
*/
function reservedTicketBalanceOf(uint256 _projectId, uint256 _reservedRate)
    external
    view
    override
    returns (uint256)
```

```javascript
/**
  @notice 
  The amount of tokens that can be claimed by the given address.

  @dev The _account must have at least _count tickets for the specified project.
  @dev If there is a funding cycle reconfiguration ballot open for the project, the project's current bonding curve is bypassed.

  @param _account The address to get an amount for.
  @param _projectId The ID of the project to get a claimable amount for.
  @param _count The number of Tickets that would be redeemed to get the resulting amount.

  @return amount The amount of tokens that can be claimed.
*/
function claimableOverflowOf(
    address _account,
    uint256 _projectId,
    uint256 _count
) public view override returns (uint256)
```

```javascript
/**
  @notice
  Whether or not a project can still print premined tickets.

  @param _projectId The ID of the project to get the status of.

  @return Boolean flag.
*/
function canPrintPreminedTickets(uint256 _projectId)
    public
    view
    override
    returns (bool)
```

### Write

```javascript
/**
  @notice 
  Deploys a project. This will mint an ERC-721 into the `_owner`'s account, configure a first funding cycle, and set up any mods.

  @dev
  Each operation withing this transaction can be done in sequence separately.

  @dev
  Anyone can deploy a project on an owner's behalf.

  @param _owner The address that will own the project.
  @param _handle The project's unique handle.
  @param _uri A link to information about the project and this funding cycle.
  @param _properties The funding cycle configuration.
    @dev _properties.target The amount that the project wants to receive in this funding cycle. Sent as a wad.
    @dev _properties.currency The currency of the `target`. Send 0 for ETH or 1 for USD.
    @dev _properties.duration The duration of the funding stage for which the `target` amount is needed. Measured in days. Send 0 for a boundless cycle reconfigurable at any time.
    @dev _properties.cycleLimit The number of cycles that this configuration should last for before going back to the last permanent. This has no effect for a project's first funding cycle.
    @dev _properties.discountRate A number from 0-200 indicating how valuable a contribution to this funding stage is compared to the project's previous funding stage.
      If it's 200, each funding stage will have equal weight.
      If the number is 180, a contribution to the next funding stage will only give you 90% of tickets given to a contribution of the same amount during the current funding stage.
      If the number is 0, an non-recurring funding stage will get made.
    @dev _configuration.ballot The new ballot that will be used to approve subsequent reconfigurations.
  @param _metadata A struct specifying the TerminalV1 specific params _bondingCurveRate, and _reservedRate.
    @dev _reservedRate A number from 0-200 indicating the percentage of each contribution's tickets that will be reserved for the project owner.
    @dev _bondingCurveRate The rate from 0-200 at which a project's Tickets can be redeemed for surplus.
      The bonding curve formula is https://www.desmos.com/calculator/sp9ru6zbpk
      where x is _count, o is _currentOverflow, s is _totalSupply, and r is _bondingCurveRate.
    @dev _reconfigurationBondingCurveRate The bonding curve rate to apply when there is an active ballot.
  @param _payoutMods Any payout mods to set.
  @param _ticketMods Any ticket mods to set.
*/
function deploy(
    address _owner,
    bytes32 _handle,
    string calldata _uri,
    FundingCycleProperties calldata _properties,
    FundingCycleMetadata calldata _metadata,
    PayoutMod[] memory _payoutMods,
    TicketMod[] memory _ticketMods
) external override
```

```javascript
/**
  @notice 
  Configures the properties of the current funding cycle if the project hasn't distributed tickets yet, or
  sets the properties of the proposed funding cycle that will take effect once the current one expires
  if it is approved by the current funding cycle's ballot.

  @dev
  Only a project's owner or a designated operator can configure its funding cycles.

  @param _projectId The ID of the project being reconfigured. 
  @param _properties The funding cycle configuration.
    @dev _properties.target The amount that the project wants to receive in this funding stage. Sent as a wad.
    @dev _properties.currency The currency of the `target`. Send 0 for ETH or 1 for USD.
    @dev _properties.duration The duration of the funding stage for which the `target` amount is needed. Measured in days. Send 0 for a boundless cycle reconfigurable at any time.
    @dev _properties.cycleLimit The number of cycles that this configuration should last for before going back to the last permanent. This has no effect for a project's first funding cycle.
    @dev _properties.discountRate A number from 0-200 indicating how valuable a contribution to this funding stage is compared to the project's previous funding stage.
      If it's 200, each funding stage will have equal weight.
      If the number is 180, a contribution to the next funding stage will only give you 90% of tickets given to a contribution of the same amount during the current funding stage.
      If the number is 0, an non-recurring funding stage will get made.
    @dev _properties.ballot The new ballot that will be used to approve subsequent reconfigurations.
  @param _metadata A struct specifying the TerminalV1 specific params _bondingCurveRate, and _reservedRate.
    @dev _metadata.reservedRate A number from 0-200 indicating the percentage of each contribution's tickets that will be reserved for the project owner.
    @dev _metadata.bondingCurveRate The rate from 0-200 at which a project's Tickets can be redeemed for surplus.
      The bonding curve formula is https://www.desmos.com/calculator/sp9ru6zbpk
      where x is _count, o is _currentOverflow, s is _totalSupply, and r is _bondingCurveRate.
    @dev _metadata.reconfigurationBondingCurveRate The bonding curve rate to apply when there is an active ballot.

  @return The ID of the funding cycle that was successfully configured.
*/
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
```

```javascript
/** 
  @notice 
  Allows a project to print tickets for a specified beneficiary before payments have been received.

  @dev 
  This can only be done if the project hasn't yet received a payment after configuring a funding cycle.

  @dev
  Only a project's owner or a designated operator can print premined tickets.

  @param _projectId The ID of the project to premine tickets for.
  @param _amount The amount to base the ticket premine off of.
  @param _currency The currency of the amount to base the ticket premine off of. 
  @param _beneficiary The address to send the printed tickets to.
  @param _memo A memo to leave with the printing.
  @param _preferUnstakedTickets If there is a preference to unstake the printed tickets.
*/
function printPreminedTickets(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    address _beneficiary,
    string memory _memo,
    bool _preferUnstakedTickets
)
```

```javascript
/**
  @notice 
  Contribute ETH to a project.

  @dev 
  Print's the project's tickets proportional to the amount of the contribution.

  @dev 
  The msg.value is the amount of the contribution in wei.

  @param _projectId The ID of the project being contribute to.
  @param _beneficiary The address to print Tickets for. 
  @param _memo A memo that will be included in the published event.
  @param _preferUnstakedTickets Whether ERC20's should be unstaked automatically if they have been issued.

  @return The ID of the funding cycle that the payment was made during.
*/
function pay(
    uint256 _projectId,
    address _beneficiary,
    string calldata _memo,
    bool _preferUnstakedTickets
) external payable override returns (uint256)
```

```javascript
/**
  @notice 
  Tap into funds that have been contributed to a project's current funding cycle.

  @dev
  Anyone can tap funds on a project's behalf.

  @param _projectId The ID of the project to which the funding cycle being tapped belongs.
  @param _amount The amount being tapped, in the funding cycle's currency.
  @param _currency The expected currency being tapped.
  @param _minReturnedWei The minimum number of wei that the amount should be valued at.

  @return The ID of the funding cycle that was tapped.
*/
function tap(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei
) external override nonReentrant returns (uint256)
```

```javascript
/**
  @notice 
  Addresses can redeem their Tickets to claim the project's overflowed ETH.

  @dev
  Only a ticket's holder or a designated operator can redeem it.

  @param _account The account to redeem tickets for.
  @param _projectId The ID of the project to which the Tickets being redeemed belong.
  @param _count The number of Tickets to redeem.
  @param _minReturnedWei The minimum amount of Wei expected in return.
  @param _beneficiary The address to send the ETH to.
  @param _preferUnstaked If the preference is to redeem tickets that have been converted to ERC-20s.

  @return amount The amount of ETH that the tickets were redeemed for.
*/
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
```

```javascript
/**
  @notice 
  Allows a project owner to migrate its funds and operations to a new contract.

  @dev
  Only a project's owner or a designated operator can migrate it.

  @param _projectId The ID of the project being migrated.
  @param _to The contract that will gain the project's funds.
*/
function migrate(uint256 _projectId, ITerminal _to)
    external
    override
    requirePermission(
        projects.ownerOf(_projectId),
        _projectId,
        Operations.Migrate
    )
    nonReentrant
```

```javascript
/** 
  @notice 
  Receives and allocates funds belonging to the specified project.

  @param _projectId The ID of the project to which the funds received belong.
*/
function addToBalance(uint256 _projectId) external payable override
```

```javascript
/**
  @notice 
  Adds to the contract addresses that projects can migrate their Tickets to.

  @dev
  Only governance can add a contract to the migration allow list.

  @param _contract The contract to allow.
*/
function allowMigration(ITerminal _contract) external override onlyGov
```

```javascript
/** 
  @notice 
  Allow the admin to change the fee. 

  @dev
  Only funding cycle reconfigurations after the new fee is set will use the new fee.
  All future funding cycles based on configurations made in the past will use the fee that was set at the time of the configuration.

  @dev
  Only governance can set a new fee.

  @param _fee The new fee percent. Out of 200.
*/
function setFee(uint256 _fee) external override onlyGov
```

```javascript
/** 
  @notice 
  Allows governance to transfer its privileges to another contract.

  @dev
  Only the currency governance can appoint a new governance.

  @param _pendingGovernance The governance to transition power to. 
    @dev This address will have to accept the responsibility in a subsequent transaction.
*/
function appointGovernance(address payable _pendingGovernance)
    external
    override
    onlyGov
```

```javascript
/** 
  @notice 
  Allows contract to accept its appointment as the new governance.
*/
function acceptGovernance() external override
```

```javascript
/**
  @notice 
  Prints all reserved tickets for a project.

  @param _projectId The ID of the project to which the reserved tickets belong.

  @return amount The amount of tickets that are being printed.
*/
function printReservedTickets(uint256 _projectId)
    public
    override
    returns (uint256 amount)
```
