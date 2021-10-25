# _configure

{% tabs %}
{% tab title="Step by step" %}
**Configures a funding cycle and stores information pertinent to the configuration.**

# Definition

```solidity
function _configure(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  uint256 _packedMetadata,
  JBOverflowAllowance[] memory _overflowAllowances,
  JBSplit[] memory _payoutSplits,
  JBSplit[] memory _reservedTokenSplits,
  bool _shouldConfigureActive
) private returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project whos funding cycles are being reconfigured.
  * `_data` is the funding cycle configuration data. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a struct specifying the controller specific params that a funding cycle can have.
  * `_overflowAllowances` is an array contraining amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal.
  * `_payoutSplits` is an array of payout splits to set.
  * `_reservedTokenSplits` is an array of reserved token splits to set.
  * `_shouldConfigureActive` is a flag indicating if the active funding cycle should be reconfigured instead of queued up a new one.
* The function is private to this contract.
* The function returns the ID of the funding cycle that was configured.

# Body

TODO
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
{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
