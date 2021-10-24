# _reservedTokenAmountFrom

{% tabs %}
{% tab title="Step by step" %}

**Gets the amount of reserved tokens currently tracked for a project given a reserved rate.**

# Definition

```solidity
function _reservedTokenAmountFrom(
  int256 _processedTokenTracker,
  uint256 _reservedRate,
  uint256 _totalEligibleTokens
) private pure returns (uint256) { ... }
```

* Arguments:
  * `_processedTokenTracker` is the tracker to make the calculation with.
  * `_reservedRate` is the reserved rate to use to make the calculation.
  * `_totalEligibleTokens` is the total amount to make the calculation with.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the reserved token amount.

# Body
TODO
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
