# \_claimableOverflowOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflowed ETH that can be claimed by the specified number of tokens.**

_If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used._

# Definition

```solidity
function _claimableOverflowOf(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _tokenCount
) private view returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get a claimable amount for.
  * `_fundingCycle` is the funding cycle during which the claimable amount applies.
  * `_tokenCount` is the number of tokens to make the calculation with.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the amount of overflowed ETH that can be claimed.

# Body

1.  Get a reference to the current overflow given the provided funding cycle. If the funding cycle specifies that the local balance should be used for redemptions, get the overflow taking only the local balance and distribution limit into account. Otherwise get the overflow taking the balances and distribution limits of all of the project's terminals into account.

    ```solidity
    // Get the amount of current overflow.
    // Use the local overflow if the funding cycle specifies that it should be used. Otherwise use the project's total overflow across all of its terminals.
    uint256 _currentOverflow = _fundingCycle.shouldUseLocalBalanceForRedemptions()
      ? _overflowDuring(_projectId, _fundingCycle)
      : _totalOverflowDuring(_projectId, _fundingCycle);
    ```

    _Internal references:_

    * [`_overflowDuring`](\_overflowduring.md)
    * [`_totalOverflowDuring`](\_totaloverflowduring.md)
2.  If there is no overflow, there's also no claimable overflow.

    ```solidity
    // If there is no overflow, nothing is claimable.
    if (_currentOverflow == 0) return 0;
    ```
3.  Get a reference to the total amount of tokens for the funding cycle's project.

    ```solidity
    // Get the total number of tokens in circulation.
    uint256 _totalSupply = tokenStore.totalSupplyOf(_projectId);
    ```

    _External references:_

    * [`totalSupplyOf`](../../../jbtokenstore/read/totalsupplyof.md)
4.  Get a reference to the total amount of outstanding reserved tokens from the project's current controller. These tokens have not yet been distributed and added to the total supply, but they must still be taken into account as part of the total when calculating the claimable amount given a set of tokens.

    ```solidity
    // Get the number of reserved tokens the project has.
    uint256 _reservedTokenAmount = directory.controllerOf(_projectId).reservedTokenBalanceOf(
      _projectId,
      _fundingCycle.reservedRate()
    );
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.reservedRate(...)`

    _External references:_

    * [`controllerOf`](../../../jbdirectory/properties/controllerof.md)
    * [`reservedTokenBalanceOf`](../../../or-controllers/jbcontroller/read/reservedtokenbalanceof.md)
5.  If there are reserved tokens, add them to the total supply for the purposes of this calculation.

    ```solidity
    // If there are reserved tokens, add them to the total supply.
    if (_reservedTokenAmount > 0) _totalSupply = _totalSupply + _reservedTokenAmount;
    ```
6.  If the calculation is being made to find the claimable amount for all of a project's tokens, return the entire current overflow.

    ```solidity
    // If the amount being redeemed is the the total supply, return the rest of the overflow.
    if (_tokenCount == _totalSupply) return _currentOverflow;
    ```
7.  Get a reference to the redemption rate that should be used in the redemption bonding curve formula. If the current funding cycle has an active ballot, use its ballot redemption rate, otherwise use the standard redemption rate. This lets project's configure different bonding curves depending on the state of pending reconfigurations. This rate is out of 10000.

    ```solidity
    // Use the ballot redemption rate if the queued cycle is pending approval according to the previous funding cycle's ballot.
    uint256 _redemptionRate = fundingCycleStore.currentBallotStateOf(_projectId) ==
      JBBallotState.Active
      ? _fundingCycle.ballotRedemptionRate()
      : _fundingCycle.redemptionRate();
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.ballotRedemptionRate(...)`\
      `.redemptionRate(...)`

    _External references:_

    * [`currentBallotStateOf`](../../../jbfundingcyclestore/read/currentballotstateof.md)
8.  If the redemption rate is 0%, nothing is claimable regardless of the amount of tokens.

    ```solidity
    // If the redemption rate is 0, nothing is claimable.
    if (_redemptionRate == 0) return 0;
    ```
9.  The redemption bonding curve formula depends on a base claimable value that is the linear proportion of the provided tokens to the total supply of tokens. Get a reference to this proportion to use in the formula.

    ```solidity
    // Get a reference to the linear proportion.
    uint256 _base = PRBMath.mulDiv(_currentOverflow, _tokenCount, _totalSupply);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv`
10. Return the claimable amount determined by a bonding curve. At a 100% bonding curve the linear base can be returned immediately, this outcome is naturally part of the curve – checking for it first could prevent an unnecessary and slightly more expensive mulDiv calculation.

    ```solidity
    // These conditions are all part of the same curve. Edge conditions are separated because fewer operation are necessary.
    if (_redemptionRate == 10000) return _base;
    return
      PRBMath.mulDiv(
        _base,
        _redemptionRate + PRBMath.mulDiv(_tokenCount, 10000 - _redemptionRate, _totalSupply),
        10000
      );
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv`
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  See docs for `claimableOverflowOf`
*/
function _claimableOverflowOf(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _tokenCount
) private view returns (uint256) {
  // Get the amount of current overflow.
  // Use the local overflow if the funding cycle specifies that it should be used. Otherwise use the project's total overflow across all of its terminals.
  uint256 _currentOverflow = _fundingCycle.shouldUseLocalBalanceForRedemptions()
    ? _overflowDuring(_projectId, _fundingCycle)
    : _totalOverflowDuring(_projectId, _fundingCycle);

  // If there is no overflow, nothing is claimable.
  if (_currentOverflow == 0) return 0;

  // Get the total number of tokens in circulation.
  uint256 _totalSupply = tokenStore.totalSupplyOf(_projectId);

  // Get the number of reserved tokens the project has.
  uint256 _reservedTokenAmount = directory.controllerOf(_projectId).reservedTokenBalanceOf(
    _projectId,
    _fundingCycle.reservedRate()
  );

  // If there are reserved tokens, add them to the total supply.
  if (_reservedTokenAmount > 0) _totalSupply = _totalSupply + _reservedTokenAmount;

  // If the amount being redeemed is the the total supply, return the rest of the overflow.
  if (_tokenCount == _totalSupply) return _currentOverflow;

  // Use the ballot redemption rate if the queued cycle is pending approval according to the previous funding cycle's ballot.
  uint256 _redemptionRate = fundingCycleStore.currentBallotStateOf(_projectId) ==
    JBBallotState.Active
    ? _fundingCycle.ballotRedemptionRate()
    : _fundingCycle.redemptionRate();
  
  // If the redemption rate is 0, nothing is claimable.
  if (_redemptionRate == 0) return 0;

  // Get a reference to the linear proportion.
  uint256 _base = PRBMath.mulDiv(_currentOverflow, _tokenCount, _totalSupply);

  // These conditions are all part of the same curve. Edge conditions are separated because fewer operation are necessary.
  if (_redemptionRate == 10000) return _base;
  return
    PRBMath.mulDiv(
      _base,
      _redemptionRate + PRBMath.mulDiv(_tokenCount, 10000 - _redemptionRate, _totalSupply),
      10000
    );
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
