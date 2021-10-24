# \_distributeToReservedTokenSplitsOf

{% tabs %}
{% tab title="Step by step" %}

**Efficiently stores a funding cycles provided configuration properties.**

### Definition

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

### Body
{% endtab %}

{% tab title="Only code" %}
```solidity
/**
  @notice
  Distributed tokens to the splits according to the specified funding cycle configuration.

  @param _fundingCycle The funding cycle to base the token distribution on.
  @param _amount The total amount of tokens to mint.

  @return leftoverAmount If the splits percents dont add up to 100%, the leftover amount is returned.
*/
function _distributeToReservedTokenSplitsOf(JBFundingCycle memory _fundingCycle, uint256 _amount)
  private
  returns (uint256 leftoverAmount)
{
  // Set the leftover amount to the initial amount.
  leftoverAmount = _amount;

  // Get a reference to the project's reserved token splits.
  JBSplit[] memory _splits = splitsStore.splitsOf(
    _fundingCycle.projectId,
    _fundingCycle.configured,
    JBSplitsGroups.RESERVED_TOKENS
  );

  //Transfer between all splits.
  for (uint256 _i = 0; _i < _splits.length; _i++) {
    // Get a reference to the split being iterated on.
    JBSplit memory _split = _splits[_i];

    // The amount to send towards the split. JBSplit percents are out of 10000.
    uint256 _tokenCount = PRBMath.mulDiv(_amount, _split.percent, 10000);

    // Mints tokens for the split if needed.
    if (_tokenCount > 0)
      tokenStore.mintFor(
        // If a projectId is set in the split, set the project's owner as the beneficiary.
        // Otherwise use the split's beneficiary.
        _split.projectId != 0 ? projects.ownerOf(_split.projectId) : _split.beneficiary,
        _fundingCycle.projectId,
        _tokenCount,
        _split.preferUnstaked
      );

    // If there's an allocator set, trigger its `allocate` function.
    if (_split.allocator != IJBSplitAllocator(address(0)))
      _split.allocator.allocate(
        _tokenCount,
        2,
        _fundingCycle.projectId,
        _split.projectId,
        _split.beneficiary,
        _split.preferUnstaked
      );

    // Subtract from the amount to be sent to the beneficiary.
    leftoverAmount = leftoverAmount - _tokenCount;

    emit DistributeToReservedTokenSplit(
      _fundingCycle.id,
      _fundingCycle.projectId,
      _split,
      _tokenCount,
      msg.sender
    );
  }
}
```
{% endtab %}
{% endtabs %}
