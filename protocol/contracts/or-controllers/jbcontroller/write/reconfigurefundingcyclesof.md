# reconfigureFundingCyclesOf

Contract:[`JBController`](../)​‌

Interface: `IJBController`

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
  JBOverflowAllowance[] memory _overflowAllowances,
  JBSplit[] memory _payoutSplits,
  JBSplit[] memory _reservedTokenSplits
)
  external
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project whos funding cycles are being reconfigured.
  * `_data` is the funding cycle configuration data. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a struct specifying the controller specific params that a funding cycle can have.
  * `_overflowAllowances` is an array contraining amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal.
  * `_payoutSplits` is an array of payout splits to set.
  * `reservedTokenSplits` is an array of reserved token splits to set.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.RECONFIGURE` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBController` interface.
* The function doesn't return the ID of the funding cycle that was configured.

# Body

TODO
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Configures the properties of the current funding cycle if the project hasn't distributed tokens yet, or sets the properties of the proposed funding cycle that will take effect once the current one expires if it is approved by the current funding cycle's ballot.

  @dev
  Only a project's owner or a designated operator can configure its funding cycles.

  @param _projectId The ID of the project whos funding cycles are being reconfigured.
  @param _data The funding cycle configuration data. These properties will remain fixed for the duration of the funding cycle.
    @dev _data.target The amount that the project wants to payout during a funding cycle. Sent as a wad (18 decimals).
    @dev _data.currency The currency of the `target`. Send 0 for ETH or 1 for USD.
    @dev _data.duration The duration of the funding cycle for which the `target` amount is needed. Measured in days. Send 0 for cycles that are reconfigurable at any time.
    @dev _data.discountRate A number from 0-10000 indicating how valuable a contribution to this funding cycle is compared to previous funding cycles.
      If it's 0, each funding cycle will have equal weight.
      If the number is 9000, a contribution to the next funding cycle will only give you 10% of tickets given to a contribution of the same amoutn during the current funding cycle.
      If the number is 10001, an non-recurring funding cycle will get made.
    @dev _data.ballot The ballot contract that will be used to approve subsequent reconfigurations. Must adhere to the IFundingCycleBallot interface.
  @param _metadata A struct specifying the controller specific params that a funding cycle can have.
    @dev _metadata.reservedRate A number from 0-200 (0-100%) indicating the percentage of each contribution's newly minted tokens that will be reserved for the token splits.
    @dev _metadata.redemptionRate The rate from 0-200 (0-100%) that tunes the bonding curve according to which a project's tokens can be redeemed for overflow.
      The bonding curve formula is https://www.desmos.com/calculator/sp9ru6zbpk
      where x is _count, o is _currentOverflow, s is _totalSupply, and r is _redemptionRate.
    @dev _metadata.ballotRedemptionRate The redemption rate to apply when there is an active ballot.
    @dev _metadata.pausePay Whether or not the pay functionality should be paused during this cycle.
    @dev _metadata.pauseWithdraw Whether or not the withdraw functionality should be paused during this cycle.
    @dev _metadata.pauseRedeem Whether or not the redeem functionality should be paused during this cycle.
    @dev _metadata.pauseMint Whether or not the mint functionality should be paused during this cycle.
    @dev _metadata.pauseBurn Whether or not the burn functionality should be paused during this cycle.
    @dev _metadata.allowTerminalMigration Whether or not the terminal migration functionality should be paused during this cycle.
    @dev _metadata.allowControllerMigration Whether or not the controller migration functionality should be paused during this cycle.
    @dev _metadata.holdFees Whether or not fees should be held to be processed at a later time during this cycle.
    @dev _metadata.useDataSourceForPay Whether or not the data source should be used when processing a payment.
    @dev _metadata.useDataSourceForRedeem Whether or not the data source should be used when processing a redemption.
    @dev _metadata.dataSource A contract that exposes data that can be used within pay and redeem transactions. Must adhere to IJBFundingCycleDataSource.
  @param _overflowAllowances An array contraining amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal.
  @param _payoutSplits An array of payout splits to set.
  @param _reservedTokenSplits An array of reserved token splits to set.

  @return The ID of the funding cycle that was successfully configured.
*/
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBOverflowAllowance[] memory _overflowAllowances,
  JBSplit[] memory _payoutSplits,
  JBSplit[] memory _reservedTokenSplits
)
  external
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256)
{
  // Make sure the metadata is validated and packed into a uint256.
  uint256 _packedMetadata = JBFundingCycleMetadataResolver.validateAndPackFundingCycleMetadata(
    _metadata
  );
  
  // All reserved tokens must be minted before configuring.
  if (uint256(_processedTokenTrackerOf[_projectId]) != tokenStore.totalSupplyOf(_projectId))
    _distributeReservedTokensOf(_projectId, '');

  // Set the this contract as the project's controller in the directory if its not already set.
  if (address(directory.controllerOf(_projectId)) == address(0))
    directory.setControllerOf(_projectId, this);

  // Configure the active project if its tokens have yet to be minted.
  bool _shouldConfigureActive = tokenStore.totalSupplyOf(_projectId) == 0;

  return
    _configure(
      _projectId,
      _data,
      _packedMetadata,
      _overflowAllowances,
      _payoutSplits,
      _reservedTokenSplits,
      _shouldConfigureActive
    );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                  | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| **`0x0f: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
{% endtab %}

{% tab title="Events" %}
| Name                                    | Data                                                                                                                                                                                                                 |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetSplit`**](../events/setsplit.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><code>Split split</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
