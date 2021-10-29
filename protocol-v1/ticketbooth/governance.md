# Governance

### Constructor

```javascript
/** 
  @param _projectId The ID of the Juicebox project being governed.
  @param _terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.
*/
constructor(uint256 _projectId, ITerminalDirectory _terminalDirectory)
```

### Write

```javascript
/** 
  @notice Gives projects using one Terminal access to migrate to another Terminal.
  @param _from The terminal to allow a new migration from.
  @param _to The terminal to allow migration to.
*/
function allowMigration(ITerminal _from, ITerminal _to) external onlyOwner
```

```javascript
/**
    @notice Adds a price feed.
    @param _prices The prices contract to add a feed to.
    @param _feed The price feed to add.
    @param _currency The currency the price feed is for.
*/
function addPriceFeed(
    IPrices _prices,
    AggregatorV3Interface _feed,
    uint256 _currency
) external onlyOwner 
```

```javascript
/** 
  @notice Sets the fee of the TerminalV1.
  @param _terminalV1 The terminalV1 to change the fee of.
  @param _fee The new fee.
*/
function setFee(ITerminalV1 _terminalV1, uint256 _fee) external onlyOwner
```

```javascript
/** 
  @notice Appoints a new governance for the specified terminalV1.
  @param _terminalV1 The terminalV1 to change the governance of.
  @param _newGovernance The address to appoint as governance.
*/
function appointGovernance(
    ITerminalV1 _terminalV1,
    address payable _newGovernance
) external onlyOwner 
```

```javascript
/** 
  @notice Accepts the offer to be the governance of a new terminalV1.
  @param _terminalV1 The terminalV1 to change the governance of.
*/
function acceptGovernance(ITerminalV1 _terminalV1) external onlyOwner 
```

