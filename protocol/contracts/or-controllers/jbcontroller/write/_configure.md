# _configure

{% tabs %}
{% tab title="Step by step" %}

**Efficiently stores a funding cycles provided configuration properties.**

# Definition

```solidity
function _packAndStoreConfigurationProperties(
  uint256 _fundingCycleId,
  uint256 _configured,
  uint256 _cycleLimit,
  IJBFundingCycleBallot _ballot,
  uint256 _duration,
  uint256 _currency,
  uint256 _fee,
  uint256 _discountRate
) private { ... }
```

* Arguments:
  * `_fundingCycleId` is the ID of the funding cycle to pack and store.
  * `_configured` is the timestamp of the configuration.
  * `_cycleLimit` is the number of cycles that this configuration should last for before going back to the last permanent.
  * `_ballot` is the ballot to use for future reconfiguration approvals.
  * `_duration` is the duration of the funding cycle.
  * `_currency` is the currency of the funding cycle.
  * `_fee` is the fee of the funding cycle.
  * `_discountRate` is the discount rate of the base funding cycle.
* The function is private to this contract.
* The function doesn't return anything.

# Body
{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice 
  Configures a funding cycle and stores information pertinent to the configuration.

  @dev
  See the docs for `launchProject` and `configureFundingCycles`.
*/
function _configure(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _packedMetadata,
  JBOverflowAllowance[] memory _overflowAllowances,
  JBSplit[] memory _payoutSplits,
  JBSplit[] memory _reservedTokenSplits,
  bool _shouldConfigureActive
) private returns (uint256) {
  // Configure the funding cycle's properties.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.configureFor(
    _projectId,
    _data,
    _packedMetadata,
    fee,
    _shouldConfigureActive
  );

  // Set payout splits if there are any.
  if (_payoutSplits.length > 0)
    splitsStore.set(
      _projectId,
      _fundingCycle.configured,
      JBSplitsGroups.ETH_PAYOUT,
      _payoutSplits
    );

  // Set token splits if there are any.
  if (_reservedTokenSplits.length > 0)
    splitsStore.set(
      _projectId,
      _fundingCycle.configured,
      JBSplitsGroups.RESERVED_TOKENS,
      _reservedTokenSplits
    );

  // Set overflow allowances if there are any.
  for (uint256 _i; _i < _overflowAllowances.length; _i++) {
    JBOverflowAllowance memory _allowance = _overflowAllowances[_i];

    // Set the overflow allowance if the value is different from the currently set value.
    if (
      _allowance.amount !=
      overflowAllowanceOf[_projectId][_fundingCycle.configured][_allowance.terminal]
    ) {
      overflowAllowanceOf[_projectId][_fundingCycle.configured][_allowance.terminal] = _allowance
        .amount;

      emit SetOverflowAllowance(_projectId, _fundingCycle.configured, _allowance, msg.sender);
    }
  }

  return _fundingCycle.id;
}
```
{% endtab %}
{% endtabs %}
