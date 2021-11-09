# launchProjectFor

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Creates a project. This will mint an ERC-721 into the specified owner's account, configure a first funding cycle, and set up any splits.**

_Each operation within this transaction can be done in sequence separately._

_Anyone can deploy a project on an owner's behalf._

# Definition

```solidity
function launchProjectFor(
  address _owner,
  bytes32 _handle,
  string calldata _metadataCid,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  JBGroupedSplits[] memory _groupedSplits,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
```

* Arguments:
  * `_owner` is the address to set as the owner of the project. The project ERC-721 will be owned by this address.
  * `_handle` is the project's unique handle. This can be updated any time by the owner of the project.
  * `_metadataCid` is a link to associate with the project. This can be updated any time by the owner of the project.
  * `_data` is a [`JBFundingCycleData`](../../../../data-structures/jbfundingcycledata.md) data structure that defines the project's first funding cycle. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a [`JBFundingCycleMetadata`](../../../../data-structures/jbfundingcyclemetadata.md) data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
  * `_fundAccessConstraints` is an array of [`JBFundAccessConstraints`](../../../../data-structures/jbfundaccessconstraints.md) data structures containing amounts that a project can distribute during each funding cycle and amounts that can be used from its own overflow on-demand for each payment terminal. The `distributionLimit` applies for each funding cycle, and the `overflowAllowance` applies for the entirety of the configuration.
  * `_groupedSplits` is an array of [`JBGroupedSplits`](../../../../data-structures/jbgroupedsplits.md) data structures containing splits to set for any number of groups.
  * `_terminals` is an array of [`IJBTerminal`](../../../../interfaces/ijbterminal.md) payment terminals to add for the project.
* The function can be accessed externally by anyone.
* The function returns the ID of the project that was launched.

# Body

1.  Make sure the reserved rate is a valid number out of 200.

    ```solidity
    // The reserved project token rate must be less than or equal to 200.
    require(_metadata.reservedRate <= 200, '0x37: BAD_RESERVED_RATE');
    ```
2.  Make sure the redemption rate is a valid number out of 200.

    ```solidity
    // The redemption rate must be between 0 and 200.
    require(_metadata.redemptionRate <= 200, '0x38: BAD_REDEMPTION_RATE');
    ```
3.  Make sure the ballot redemption rate is less than 100%.

    ```solidity
    // The ballot redemption rate must be less than or equal to 200.
    require(_metadata.ballotRedemptionRate <= 200, '0x39: BAD_BALLOT_REDEMPTION_RATE');
    ```
4.  Create the project. This will mint an ERC-721 in the `_owners` wallet representing ownership over the project.

    ```solidity
    // Create the project for into the wallet of the message sender.
    projectId = projects.createFor(_owner, _handle, _metadataCid);
    ```

    _External references:_

    * [`createFor`](../../../jbprojects/write/createfor.md)
5.  Set this controller as the controller of the project.

    ```solidity
    // Set the this contract as the project's controller in the directory.
    directory.setControllerOf(projectId, this);
    ```

    _External references:_

    * [`setControllerOf`](../../../jbdirectory/write/setcontrollerof.md)
6.  If terminals were provided, add them to the list of terminals the project can accept funds through.

    ```solidity
    // Add the provided terminals to the list of terminals.
    if (_terminals.length > 0) directory.addTerminalsOf(projectId, _terminals);
    ```

    _External references:_

    * [`addTerminalsOf`](../../../jbdirectory/write/addterminalsof.md)
8.  Configure the project's funding cycle, fund access constraints, and splits.

    ```solidity
    _configure(projectId, _data, _metadata, _fundAccessConstraints, _groupedSplits);
    ```

    _Internal references:_

    * [`_configure`](\_configure.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Creates a project. This will mint an ERC-721 into the specified owner's account, configure a first funding cycle, and set up any splits.

  @dev
  Each operation within this transaction can be done in sequence separately.

  @dev
  Anyone can deploy a project on an owner's behalf.

  @param _owner The address to set as the owner of the project. The project ERC-721 will be owned by this address.
  @param _handle The project's unique handle. This can be updated any time by the owner of the project.
  @param _metadataCid A link to associate with the project. This can be updated any time by the owner of the project.
  @param _data A JBFundingCycleData data structure that defines the project's first funding cycle. These properties will remain fixed for the duration of the funding cycle.
    @dev _data.target The amount that the project wants to payout during a funding cycle. Sent as a wad (18 decimals).
    @dev _data.currency The currency of the `target`. Send 0 for ETH or 1 for USD.
    @dev _data.duration The duration of the funding cycle for which the `target` amount is needed. Measured in days. Send 0 for cycles that are reconfigurable at any time.
    @dev _data.discountRate A number from 0-1000000000 indicating how valuable a contribution to this funding cycle is compared to previous funding cycles.
      If it's 0, each funding cycle will have equal weight.
      If the number is 900000000, a contribution to the next funding cycle will only give you 10% of tickets given to a contribution of the same amoutn during the current funding cycle.
      If the number is 1000000001, an non-recurring funding cycle will get made.
    @dev _data.ballot The ballot contract that will be used to approve subsequent reconfigurations. Must adhere to the IFundingCycleBallot interface.
  @param _metadata A JBFundingCycleMetadata data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
    @dev _metadata.reservedRate A number from 0-200 (0-100%) indicating the percentage of each contribution's newly minted tokens that will be reserved for the token splits.
    @dev _metadata.redemptionRate The rate from 0-200 (0-100%) that tunes the bonding curve according to which a project's tokens can be redeemed for overflow.
      The bonding curve formula is https://www.desmos.com/calculator/sp9ru6zbpk
      where x is _count, o is _currentOverflow, s is _totalSupply, and r is _redemptionRate.
    @dev _metadata.ballotRedemptionRate The redemption rate to apply when there is an active ballot.
    @dev _metadata.pausePay Whether or not the pay functionality should be paused during this cycle.
    @dev _metadata.pauseWithdrawals Whether or not the withdraw functionality should be paused during this cycle.
    @dev _metadata.pauseRedeem Whether or not the redeem functionality should be paused during this cycle.
    @dev _metadata.pauseMint Whether or not the mint functionality should be paused during this cycle.
    @dev _metadata.pauseBurn Whether or not the burn functionality should be paused during this cycle.
    @dev _metadata.allowTerminalMigration Whether or not the terminal migration functionality should be paused during this cycle.
    @dev _metadata.allowControllerMigration Whether or not the controller migration functionality should be paused during this cycle.
    @dev _metadata.holdFees Whether or not fees should be held to be processed at a later time during this cycle.
    @dev _metadata.useDataSourceForPay Whether or not the data source should be used when processing a payment.
    @dev _metadata.useDataSourceForRedeem Whether or not the data source should be used when processing a redemption.
    @dev _metadata.dataSource A contract that exposes data that can be used within pay and redeem transactions. Must adhere to IJBFundingCycleDataSource.
  @param _fundAccessConstraints An array containing amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal. The `distributionLimit` applies for each funding cycle, and the `overflowAllowance` applies for the entirety of the configuration.
  @param _groupedSplits An array of splits to set for any number of group.
  @param _terminals Payment terminals to add for the project.

  @return projectId The ID of the project.
*/
function launchProjectFor(
  address _owner,
  bytes32 _handle,
  string calldata _metadataCid,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  JBGroupedSplits[] memory _groupedSplits,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) {
  // The reserved project token rate must be less than or equal to 200.
  require(_metadata.reservedRate <= 200, '0x37: BAD_RESERVED_RATE');

  // The redemption rate must be between 0 and 200.
  require(_metadata.redemptionRate <= 200, '0x38: BAD_REDEMPTION_RATE');

  // The ballot redemption rate must be less than or equal to 200.
  require(_metadata.ballotRedemptionRate <= 200, '0x39: BAD_BALLOT_REDEMPTION_RATE');

  // Create the project for into the wallet of the message sender.
  projectId = projects.createFor(_owner, _handle, _metadataCid);

  // Set the this contract as the project's controller in the directory.
  directory.setControllerOf(projectId, this);

  // Add the provided terminals to the list of terminals.
  if (_terminals.length > 0) directory.addTerminalsOf(projectId, _terminals);

  _configure(projectId, _data, _metadata, _fundAccessConstraints, _groupedSplits);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                                 | Description                                                |
| -------------------------------------- | ---------------------------------------------------------- |
| **`0x37: BAD_RESERVED_RATE`**          | Thrown if the reserved rate is greater than 100%.          |
| **`0x38: BAD_REDEMPTION_RATE`**        | Thrown if the redemption rate is greater than 100%.        |
| **`0x39: BAD_BALLOT_REDEMPTION_RATE`** | Thrown if the ballot redemption rate is greater than 100%. |
{% endtab %}

{% tab title="Events" %}
| Name                                                            | Data                                                                                                                                                                                                                                                                            |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetFundAccessConstraints`**](../events/setfundaccessconstraints.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed configuration</code></li><li><a href="../../../../data-structures/jboverflowallowance.md"><code>JBFundAccessConstraints</code></a><code>constraints</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
