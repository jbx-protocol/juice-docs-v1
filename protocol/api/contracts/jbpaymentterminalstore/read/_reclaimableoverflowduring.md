# \_reclaimableOverflowDuring

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflowed tokens from a terminal that can be reclaimed by the specified number of tokens  when measured from the specified.**

_If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used._

#### Definition

```solidity
function _reclaimableOverflowDuring(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _tokenCount,
  uint256 _overflow
) private view returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get the reclaimable overflow amount for.
  * `_fundingCycle` is the funding cycle during which reclaimable overflow is being calculated.
  * `_tokenCount` is the number of tokens to make the calculation with, as a fixed point number with 18 decimals.
  * `_overflow` is the amount of overflow to make the calculation with.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns the amount of overflowed tokens that can be reclaimed.

#### Body

1.  Get a reference to the total amount of outstanding tokens from the project's current controller.   

    ```solidity
    // Get the number of outstanding tokens the project has.
    uint256 _totalSupply = directory.controllerOf(_projectId).totalOutstandingTokensOf(
      _projectId,
      _fundingCycle.reservedRate()
    );
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.reservedRate(...)`

    _External references:_

    * [`controllerOf`](../../jbdirectory/properties/controllerof.md)
    * [`totalOutstandingTokensOf`](../../or-controllers/jbcontroller/read/totaloutstandingtokensof.md)
3.  If there are reserved tokens, add them to the total supply for the purposes of this calculation.

    ```solidity
    // If there are reserved tokens, add them to the total supply.
    if (_reservedTokenAmount > 0) _totalSupply = _totalSupply + _reservedTokenAmount;
    ```
4.  If the calculation is being made to find the claimable amount for all of a project's tokens, return the entire current overflow.

    ```solidity
    // If the amount being redeemed is the total supply, return the rest of the overflow.
    if (_tokenCount == _totalSupply) return _overflow;
    ```
5.  Get a reference to the redemption rate that should be used in the redemption bonding curve formula. If the current funding cycle has an active ballot, use its ballot redemption rate, otherwise use the standard redemption rate. This lets projects configure different bonding curves depending on the state of pending reconfigurations. 

    ```solidity
    // Use the ballot redemption rate if the queued cycle is pending approval according to the previous funding cycle's ballot.
    uint256 _redemptionRate = fundingCycleStore.currentBallotStateOf(_projectId) ==
      JBBallotState.Active
      ? _fundingCycle.ballotRedemptionRate()
      : _fundingCycle.redemptionRate();
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.ballotRedemptionRate(...)`\
      `.redemptionRate(...)`

    _External references:_

    * [`currentBallotStateOf`](../../jbfundingcyclestore/read/currentballotstateof.md)
6.  If the redemption rate is 0%, nothing is claimable regardless of the amount of tokens.

    ```solidity
    // If the redemption rate is 0, nothing is claimable.
    if (_redemptionRate == 0) return 0;
    ```
7.  The redemption bonding curve formula depends on a base claimable value that is the linear proportion of the provided tokens to the total supply of tokens. Get a reference to this proportion to use in the formula.

    ```solidity
    // Get a reference to the linear proportion.
    uint256 _base = PRBMath.mulDiv(_overflow, _tokenCount, _totalSupply);
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`
8.  Return the claimable amount determined by a bonding curve. At a 100% bonding curve the linear base can be returned immediately, this outcome is naturally part of the curve – checking for it first could prevent an unnecessary and slightly more expensive mulDiv calculation.

    ```solidity
    // These conditions are all part of the same curve. Edge conditions are separated because fewer operation are necessary.
    if (_redemptionRate == JBConstants.MAX_REDEMPTION_RATE) return _base;
    
    return
      PRBMath.mulDiv(
        _base,
        _redemptionRate +
          PRBMath.mulDiv(
            _tokenCount,
            JBConstants.MAX_REDEMPTION_RATE - _redemptionRate,
            _totalSupply
          ),
        JBConstants.MAX_REDEMPTION_RATE
      );
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`
    * [`JBConstants`](../../../libraries/jbconstants.md)
      * `.MAX_REDEMPTION_RATE`
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  The amount of overflowed tokens from a terminal that can be reclaimed by the specified number of tokens when measured from the specified.

  @dev 
  If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used.

  @param _projectId The ID of the project to get the reclaimable overflow amount for.
  @param _fundingCycle The funding cycle during which reclaimable overflow is being calculated.
  @param _tokenCount The number of tokens to make the calculation with, as a fixed point number with 18 decimals.
  @param _overflow The amount of overflow to make the calculation with.

  @return The amount of overflowed tokens that can be reclaimed.
*/
function _reclaimableOverflowDuring(
  uint256 _projectId,
  JBFundingCycle memory _fundingCycle,
  uint256 _tokenCount,
  uint256 _overflow
) private view returns (uint256) {
  // Get the number of outstanding tokens the project has.
  uint256 _totalSupply = directory.controllerOf(_projectId).totalOutstandingTokensOf(
    _projectId,
    _fundingCycle.reservedRate()
  );

  // If there are reserved tokens, add them to the total supply.
  if (_reservedTokenAmount > 0) _totalSupply = _totalSupply + _reservedTokenAmount;

  // If the amount being redeemed is the total supply, return the rest of the overflow.
  if (_tokenCount == _totalSupply) return _overflow;

  // Use the ballot redemption rate if the queued cycle is pending approval according to the previous funding cycle's ballot.
  uint256 _redemptionRate = fundingCycleStore.currentBallotStateOf(_projectId) ==
    JBBallotState.Active
    ? _fundingCycle.ballotRedemptionRate()
    : _fundingCycle.redemptionRate();

  // If the redemption rate is 0, nothing is claimable.
  if (_redemptionRate == 0) return 0;

  // Get a reference to the linear proportion.
  uint256 _base = PRBMath.mulDiv(_overflow, _tokenCount, _totalSupply);

  // These conditions are all part of the same curve. Edge conditions are separated because fewer operation are necessary.
  if (_redemptionRate == JBConstants.MAX_REDEMPTION_RATE) return _base;

  return
    PRBMath.mulDiv(
      _base,
      _redemptionRate +
        PRBMath.mulDiv(
          _tokenCount,
          JBConstants.MAX_REDEMPTION_RATE - _redemptionRate,
          _totalSupply
        ),
      JBConstants.MAX_REDEMPTION_RATE
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
