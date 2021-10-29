# Write

### acceptGovernance

Allows contract to accept its appointment as the new governance.\
Only the pending governance can accept.

```
function acceptGovernance() external;
```

### addToBalance

Receives and allocates funds belonging to the specified project.

**Params:**

* **\_projectId**: The ID of the project to which the funds received belong.

```
function addToBalance(uint256 _projectId) external payable;
```

### allowMigration

Adds to the contract addresses that projects can migrate their Tickets to.

Only governance can add a contract to the migration allow list.

**Params:**

* **\_contract**: The contract to allow.

```
function allowMigration(ITerminal _contract) external;
```

### configure

Configures the properties of the current funding cycle if the project hasn't distributed tickets yet, or sets the properties of the proposed funding cycle that will take effect once the current one expires if it is approved by the current funding cycle's ballot.

Only a project's owner or a designated operator can configure its funding cycles.

**Params:**

* **\_projectId**: The ID of the project being reconfigured.
* **\_properties**: The funding cycle configuration.
  * **\_properties.target**: The amount that the project wants to receive in this funding stage. Sent as a wad.
  * **\_properties.currency**: The currency of the \`target\`. Send 0 for ETH or 1 for USD.
  * **\_properties.duration**: The duration of the funding stage for which the \`target\` amount is needed. Measured in days. Send 0 for a boundless cycle reconfigurable at any time.
  * **\_properties.cycleLimit**: The number of cycles that this configuration should last for before going back to the last permanent. This has no effect for a project's first funding cycle.
  * **\_properties.discountRate**: A number from 0-200 indicating how valuable a contribution to this funding stage is compared to the project's previous funding stage.
    * If it's 200, each funding stage will have equal weight.
    * If the number is 180, a contribution to the next funding stage will only give you 90% of tickets given to a contribution of the same amount during the current funding stage.
    * If the number is 0, an non-all transactions except the constructor are write functions recurring funding stage will get made.
  * **\_properties.ballot**: The new ballot that will be used to approve subsequent reconfigurations.
* **\_metadata**: A struct specifying the TerminalV1 specific params \_bondingCurveRate, and \_reservedRate.
*
  * **\_metadata.reservedRate**: A number from 0-200 indicating the percentage of each contribution's tickets that will be reserved for the project owner.
  * **\_metadata.bondingCurveRate**: The rate from 0-200 at which a project's Tickets can be redeemed for surplus. The bonding curve formula can be found [here](https://www.desmos.com/calculator/sp9ru6zbpk), where x is \_count, o is \_currentOverflow, s is \_totalSupply, and r is \_bondingCurveRate.
  * **\_metadata.reconfigurationBondingCurveRate**: The bonding curve rate to apply when there is an active ballot.

**Returns:** The ID of the funding cycle that was successfully configured.

```lua
    function configure(
        uint256 _projectId,
        FundingCycleProperties calldata _properties,
        FundingCycleMetadata calldata _metadata,
        PayoutMod[] memory _payoutMods,
        TicketMod[] memory _ticketMods
    ) external returns (uint256);
```

### deploy

Deploys a project. This will mint an ERC-721 into the \_owner's account, configure a first funding cycle, and set up any mods.

Each operation within this transaction can be done in sequence separately\
Anyone can deploy a project on an owner's behalf.

**Params:**

* **\_owner**: The address that will own the project.
* **\_handle**: The project's unique handle.
* **\_uri**: A link to information about the project and this funding cycle.
* **\_properties**: The funding cycle configuration.
  * **\_properties.target**: The amount that the project wants to receive in this funding cycle. Sent as a wad.
  * **\_properties.currency**: The currency of the \`target\`. Send 0 for ETH or 1 for USD.
  * **\_properties.duration**: The duration of the funding stage for which the \`target\` amount is needed. Measured in days. Send 0 for a boundless cycle reconfigurable at any time.
  * **\_properties.cycleLimit**: The number of cycles that this configuration should last for before going back to the last permanent. This has no effect for a project's first funding cycle.
  * **\_properties.discountRate**: A number from 0-200 indicating how valuable a contribution to this funding stage is compared to the project's previous funding stage.
  * If it's 200, each funding stage will have equal weight.
  * If the number is 180, a contribution to the next funding stage will only give you 90% of tickets given to a contribution of the same amount during the current funding stage.
  * If the number is 0, an non-recurring funding stage will get made.
  * **\_properties.ballot**: The new ballot that will be used to approve subsequent reconfigurations.
* **\_metadata**: A struct specifying the TerminalV1 specific params \_bondingCurveRate, and \_reservedRate.
  * **\_metadata.reservedRate**: A number from 0-200 indicating the percentage of each contribution's tickets that will be reserved for the project owner.
  * **\_metadata.bondingCurveRate**: The rate from 0-200 at which a project's Tickets can be redeemed for surplus.
    *   The bonding curve formula can be found [here](https://www.desmos.com/calculator/sp9ru6zbpk).

        where x is \_count, o is \_currentOverflow, s is \_totalSupply, and r is \_bondingCurveRate.
  * **\_metadata.reconfigurationBondingCurveRate**: The bonding curve rate to apply when there is an active ballot.
* **\_payoutMods**: Any payout mods to set.
* **\_ticketMods**: Any ticket mods to set.

```lua
    function deploy(
        address _owner,
        bytes32 _handle,
        string calldata _uri,
        FundingCycleProperties calldata _properties,
        FundingCycleMetadata calldata _metadata,
        PayoutMod[] memory _payoutMods,
        TicketMod[] memory _ticketMods
    ) external;
```

### migrate

Allows a project owner to migrate its funds and operations to a new contract.

Only a project's owner or a designated operator can migrate it.

**Params:**

* **\_projectId**: The ID of the project being migrated.
* **\_to**: The contract that will gain the project's funds.

```
function migrate(uint256 _projectId, ITerminal _to) external;
```

### onlyGov

Allows governance to transfer its privileges to another contract.

Only the current governance can appoint a new governance.

**Params:**

* **\_pendingGovernance**: The governance to transition power to.
  * This address will have to accept the responsibility in a subsequent transaction.

```
function appointGovernance(address payable _pendingGovernance) external;
```

###

### pay

Contribute ETH to a project.

Print's the project's tickets proportional to the amount of the contribution.\
The msg.value is the amount of the contribution in wei.

**Params:**

* **\_projectId**: The ID of the project being contribute to.
* **\_beneficiary**: The address to print Tickets for. \_memo A memo that will be included in the published event.
* **\_preferUnstakedTickets**: Whether ERC20's should be unstaked automatically if they have been issued.

**Returns:** The ID of the funding cycle that the payment was made durin

```
function pay(
    uint256 _projectId,
    address _beneficiary,
    string calldata _memo,
    bool _preferUnstakedTickets
) external payable returns (uint256 fundingCycleId);
```

### printPreminedTickets

Allows a project to print tickets for a specified beneficiary before payments have been received. @dev

This can only be done if the project hasn't yet received a payment after configuring a funding cycle.\
Only a project's owner or a designated operator can print premined tickets.

* **\_projectId**: The ID of the project to premine tickets for.
* **\_amount**: The amount to base the ticket premine off of.
* **\_currency**: The currency of the amount to base the ticket premine off of.
* **\_beneficiary**: The address to send the printed tickets to.
* **\_memo**: A memo to leave with the printing.
* **\_preferUnstakedTickets**: If there is a preference to unstake the printed tickets.

```lua
function printPreminedTickets(
        uint256 _projectId,
        uint256 _amount,
        uint256 _currency,
        address _beneficiary,
        string memory _memo,
        bool _preferUnstakedTickets
) external;
```

### printReservedTickets

Prints all reserved tickets for a project.

**Params:**

* **\_projectId**: The ID of the project to which the reserved tickets belong.

**Returns:** The amount of tickets that are being printed

```
function printReservedTickets(uint256 _projectId)
    external
    returns (uint256 reservedTicketsToPrint);
```

### redeem

Addresses can redeem their Tickets to claim the project's overflowed ETH.

Only a ticket's holder or a designated operator can redeem it.

**Params:**

* **\_account**: The account to redeem tickets for.
* **\_projectId**: The ID of the project to which the Tickets being redeemed belong.
* **\_count**: The number of Tickets to redeem.
* **\_minReturnedWei**: The minimum amount of Wei expected in return.
* **\_beneficiary**: The address to send the ETH to.
* **\_preferUnstaked**: If the preference is to redeem tickets that have been converted to ERC-20s.

**Returns:** The amount of ETH that the tickets were redeemed for.

```
    function redeem(
        address _account,
        uint256 _projectId,
        uint256 _amount,
        uint256 _minReturnedWei,
        address payable _beneficiary,
        bool _preferUnstaked
    ) external returns (uint256 returnAmount);
```

### **setFee**

Allow the admin to change the fee.

Only funding cycle reconfigurations after the new fee is set will use the new fee. All future funding cycles based on configurations made in the past will use the fee that was set at the time of the configuration.\
Only governance can set a new fee.

**Params:**

* **\_fee**: The new fee percent. Out of 200.

```
function setFee(uint256 _fee) external;
```

###

### **tap**

Tap into funds that have been contributed to a project's current funding cycle.

Anyone can tap funds on a project's behalf.

**Params:**

* **\_projectId**: The ID of the project to which the funding cycle being tapped belongs.
* **\_amount**: The amount being tapped, in the funding cycle's currency.
* **\_currency**: The expected currency being tapped.
* **\_minReturnedWei**: The minimum number of wei that the amount should be valued at.

**Returns:** The ID of the funding cycle that was tapped.

```
function tap(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedWei
) external returns (uint256);
```

### TO-DO

Write functions yet to be documented.

```
function allowMigration(ITerminal _contract) external;

function appointGovernance(address payable _pendingGovernance) external;
```
