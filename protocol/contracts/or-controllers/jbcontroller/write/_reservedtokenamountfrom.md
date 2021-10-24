# _reservedTokenAmountFrom

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
  Gets the amount of reserved tokens currently tracked for a project given a reserved rate.

  @param _processedTokenTracker The tracker to make the calculation with.
  @param _reservedRate The reserved rate to use to make the calculation.
  @param _totalEligibleTokens The total amount to make the calculation with.

  @return amount reserved token amount.
*/
function _reservedTokenAmountFrom(
  int256 _processedTokenTracker,
  uint256 _reservedRate,
  uint256 _totalEligibleTokens
) private pure returns (uint256) {
  // Get a reference to the amount of tokens that are unprocessed.
  uint256 _unprocessedTokenBalanceOf = _processedTokenTracker >= 0 // preconfigure tokens shouldn't contribute to the reserved token amount.
    ? _totalEligibleTokens - uint256(_processedTokenTracker)
    : _totalEligibleTokens + uint256(-_processedTokenTracker);

  // If there are no unprocessed tokens, return.
  if (_unprocessedTokenBalanceOf == 0) return 0;

  // If all tokens are reserved, return the full unprocessed amount.
  if (_reservedRate == 200) return _unprocessedTokenBalanceOf;

  return
    PRBMath.mulDiv(_unprocessedTokenBalanceOf, 200, 200 - _reservedRate) -
    _unprocessedTokenBalanceOf;
}
```
{% endtab %}
{% endtabs %}
