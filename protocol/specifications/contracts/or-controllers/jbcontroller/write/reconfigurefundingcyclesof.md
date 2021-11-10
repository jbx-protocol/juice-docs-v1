# reconfigureFundingCyclesOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Configures the properties of the current funding cycle if the project hasn't distributed tokens yet, or sets the properties of the proposed funding cycle that will take effect once the current one expires if it is approved by the current funding cycle's ballot.**

_Only a project's owner or a designated operator can configure its funding cycles._

# Definition

```solidity
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project whose funding cycles are being reconfigured.
  * `_data` is a [`JBFundingCycleData`](../../../../data-structures/jbfundingcycledata.md) data structure that defines the project's funding cycle that will be queued. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a [`JBFundingCycleMetadata`](../../../../data-structures/jbfundingcyclemetadata.md) data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
  * `_groupedSplits` is an array of [`JBGroupedSplits`](../../../../data-structures/jbgroupedsplits.md) data structures containing splits to set for any number of groups.
  * `_fundAccessConstraints` is an array of [`JBFundAccessConstraints`](../../../../data-structures/jbfundaccessconstraints.md) data structures containing amounts that a project can distribute during each funding cycle and amounts that can be used from its own overflow on-demand for each payment terminal. The `distributionLimit` applies for each funding cycle, and the `overflowAllowance` applies for the entirety of the configuration.
* Through the [`requirePermission`](../../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.RECONFIGURE` permission by the project owner for the provided `_projectId`.
* The function returns the configuration of the funding cycle that was successfully updated.

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
4.  Configure the project's funding cycle, fund access constraints, and splits.

    ```solidity
    return _configure(_projectId, _data, _metadata, _groupedSplits, _fundAccessConstraints);
    ```

    _Internal references:_

    * [`_configure`](\_configure.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Configures the properties of the current funding cycle if the project hasn't distributed tokens yet, or
  sets the properties of the proposed funding cycle that will take effect once the current one expires
  if it is approved by the current funding cycle's ballot.

  @dev
  Only a project's owner or a designated operator can configure its funding cycles.

  @param _projectId The ID of the project whose funding cycles are being reconfigured.
  @param _data A JBFundingCycleData data structure that defines the project's funding cycle that will be queued. These properties will remain fixed for the duration of the funding cycle.
    @dev _data.target The amount that the project wants to payout during a funding cycle. Sent as a wad (18 decimals).
    @dev _data.currency The currency of the `target`. Send 0 for ETH or 1 for USD.
    @dev _data.duration The duration of the funding cycle for which the `target` amount is needed. Measured in days. Send 0 for cycles that are reconfigurable at any time.
    @dev _data.weight The weight of the funding cycle.
      This number is interpreted as a wad, meaning it has 18 decimal places.
      The protocol uses the weight to determine how many tokens to mint upon receiving a payment during a funding cycle.
      A value of 0 means that the weight should be inherited and potentially discounted from the currently active cycle if possible. Otherwise a weight of 0 will be used.
      A value of 1 means that no tokens should be minted regardless of how many ETH was paid. The protocol will set the stored weight value to 0.
      A value of 1 X 10^18 means that one token should be minted per ETH received.
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
  @param _groupedSplits An array of splits to set for any number of group.
  @param _fundAccessConstraints An array containing amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal. The `distributionLimit` applies for each funding cycle, and the `overflowAllowance` applies for the entirety of the configuration.

  @return The configuration of the funding cycle that was successfully updated.
*/
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256)
{
  // The reserved project token rate must be less than or equal to 200.
  require(_metadata.reservedRate <= 200, '0x51: BAD_RESERVED_RATE');

  // The redemption rate must be between 0 and 200.
  require(_metadata.redemptionRate <= 200, '0x52: BAD_REDEMPTION_RATE');

  // The ballot redemption rate must be less than or equal to 200.
  require(_metadata.ballotRedemptionRate <= 200, '0x53: BAD_BALLOT_REDEMPTION_RATE');

  return _configure(_projectId, _data, _metadata, _groupedSplits, _fundAccessConstraints);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                                 | Description                                                |
| -------------------------------------- | ---------------------------------------------------------- |
| **`0x51: BAD_RESERVED_RATE`**          | Thrown if the reserved rate is greater than 100%.          |
| **`0x52: BAD_REDEMPTION_RATE`**        | Thrown if the redemption rate is greater than 100%.        |
| **`0x53: BAD_BALLOT_REDEMPTION_RATE`** | Thrown if the ballot redemption rate is greater than 100%. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                                | Data                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
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
