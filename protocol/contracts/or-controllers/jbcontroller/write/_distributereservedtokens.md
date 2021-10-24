# _distributeReservedTokens

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

{% tab title="Code" %}
```solidity
/**
  @notice 
  See docs for `distributeReservedTokens`
*/
function _distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  private
  returns (uint256 count)
{
  // Get the current funding cycle to read the reserved rate from.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // There aren't any reserved tokens to mint and distribute if there is no funding cycle.
  if (_fundingCycle.number == 0) return 0;

  // Get a reference to new total supply of tokens before minting reserved tokens.
  uint256 _totalTokens = tokenStore.totalSupplyOf(_projectId);

  // Get a reference to the number of tokens that need to be minted.
  count = _reservedTokenAmountFrom(
    _processedTokenTrackerOf[_projectId],
    _fundingCycle.reservedRate(),
    _totalTokens
  );

  // Set the tracker to be the new total supply.
  _processedTokenTrackerOf[_projectId] = int256(_totalTokens + count);

  // Get a reference to the project owner.
  address _owner = projects.ownerOf(_projectId);

  // Distribute tokens to splits and get a reference to the leftover amount to mint after all splits have gotten their share.
  uint256 _leftoverTokenCount = count == 0
    ? 0
    : _distributeToReservedTokenSplitsOf(_fundingCycle, count);

  // Mint any leftover tokens to the project owner.
  if (_leftoverTokenCount > 0) tokenStore.mintFor(_owner, _projectId, _leftoverTokenCount, false);

  emit DistributeReservedTokens(
    _fundingCycle.id,
    _projectId,
    _owner,
    count,
    _leftoverTokenCount,
    _memo,
    msg.sender
  );
}
```
{% endtab %}
{% endtabs %}
