# Funding Cycles

### Constructor

```javascript
/** 
  @param _terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.
*/
constructor(ITerminalDirectory _terminalDirectory)
    TerminalUtility(_terminalDirectory)
```

### Read

```javascript
/**
    @notice 
    Get the funding cycle with the given ID.

    @param _fundingCycleId The ID of the funding cycle to get.

    @return _fundingCycle The funding cycle.
*/
function get(uint256 _fundingCycleId)
    external
    view
    override
    returns (FundingCycle memory)
```

```javascript
/**
    @notice 
    The funding cycle that's next up for a project, and therefor not currently accepting payments.

    @dev 
    This runs roughly similar logic to `_configurable`.

    @param _projectId The ID of the project being looked through.

    @return _fundingCycle The queued funding cycle.
*/
function queuedOf(uint256 _projectId)
    external
    view
    override
    returns (FundingCycle memory)
```

```javascript
/**
    @notice 
    The funding cycle that is currently active for the specified project.

    @dev 
    This runs very similar logic to `_tappable`.

    @param _projectId The ID of the project being looked through.

    @return fundingCycle The current funding cycle.
*/
function currentOf(uint256 _projectId)
    external
    view
    override
    returns (FundingCycle memory fundingCycle)
```

```javascript
/** 
  @notice 
  The currency ballot state of the project.

  @param _projectId The ID of the project to check for a pending reconfiguration.

  @return The current ballot's state.
*/
function currentBallotStateOf(uint256 _projectId)
    external
    view
    override
    returns (BallotState)
```

### Write

```javascript
/**
    @notice 
    Configures the next eligible funding cycle for the specified project.

    @dev
    Only a project's current terminal can configure its funding cycles.

    @param _projectId The ID of the project being reconfigured.
    @param _properties The funding cycle configuration.
      @dev _properties.target The amount that the project wants to receive in each funding cycle. 18 decimals.
      @dev _properties.currency The currency of the `_target`. Send 0 for ETH or 1 for USD.
      @dev _properties.duration The duration of the funding cycle for which the `_target` amount is needed. Measured in days. 
        Set to 0 for no expiry and to be able to reconfigure anytime.
      @dev _cycleLimit The number of cycles that this configuration should last for before going back to the last permanent. This does nothing for a project's first funding cycle.
      @dev _properties.discountRate A number from 0-200 indicating how valuable a contribution to this funding cycle is compared to previous funding cycles.
        If it's 0, each funding cycle will have equal weight.
        If the number is 100, a contribution to the next funding cycle will only give you 90% of tickets given to a contribution of the same amount during the current funding cycle.
        If the number is 200, a contribution to the next funding cycle will only give you 80% of tickets given to a contribution of the same amoutn during the current funding cycle.
        If the number is 201, an non-recurring funding cycle will get made.
      @dev _ballot The new ballot that will be used to approve subsequent reconfigurations.
    @param _metadata Data to associate with this funding cycle configuration.
    @param _fee The fee that this configuration will incure when tapping.
    @param _configureActiveFundingCycle If a funding cycle that has already started should be configurable.

    @return fundingCycle The funding cycle that the configuration will take effect during.
*/
function configure(
    uint256 _projectId,
    FundingCycleProperties calldata _properties,
    uint256 _metadata,
    uint256 _fee,
    bool _configureActiveFundingCycle
)
    external
    override
    onlyTerminal(_projectId)
    returns (FundingCycle memory fundingCycle)
```

```javascript
/** 
  @notice 
  Tap funds from a project's currently tappable funding cycle.

  @dev
  Only a project's current terminal can tap funds for its funding cycles.

  @param _projectId The ID of the project being tapped.
  @param _amount The amount being tapped.

  @return fundingCycle The tapped funding cycle.
*/
function tap(uint256 _projectId, uint256 _amount)
    external
    override
    onlyTerminal(_projectId)
    returns (FundingCycle memory fundingCycle)
```
