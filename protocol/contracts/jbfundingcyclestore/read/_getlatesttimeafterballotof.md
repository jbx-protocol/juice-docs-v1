# \_getLatestTimeAfterBallotOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The time after the ballot of the provided funding cycle has expired.**\
****\
****_If the ballot ends in the past, the current block timestamp will be returned._

Definition:

```solidity
function _getLatestTimeAfterBallotOf(JBFundingCycle memory _fundingCycle, uint256 _from)
  private
  view
  returns (uint256) { ... } 
```

* `_fundingCycle` is the ID funding cycle to make the calculation from.
* `_from` is the time from which the ballot duration should be calculated.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the time.



1.  If the provided `_fundingCycle` has no ballot, return the current timestamp.

    ```solidity
    // If the provided funding cycle has no ballot, return the current timestamp.
    if (_fundingCycle.ballot == IJBFundingCycleBallot(address(0))) return block.timestamp;
    ```


2.  Get a reference to the ballot's expiration, which is the ballot's duration measured `_from` the provided value.

    ```solidity
    // Get a reference to the time the ballot ends.
    uint256 _ballotExpiration = _from + _fundingCycle.ballot.duration();
    ```


3.  If the ballot ends in past, return the current timestamp. Otherwise return the ballot's expiration.

    ```solidity
    // If the ballot ends in past, return the current timestamp. Otherwise return the ballot's expiration.
    return block.timestamp > _ballotExpiration ? block.timestamp : _ballotExpiration;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  The time after the ballot of the provided funding cycle has expired.

  @dev
  If the ballot ends in the past, the current block timestamp will be returned.

  @param _fundingCycle The ID funding cycle to make the calculation from.
  @param _from The time from which the ballot duration should be calculated.

  @return The time when the ballot duration ends.
*/
function _getLatestTimeAfterBallotOf(JBFundingCycle memory _fundingCycle, uint256 _from)
  private
  view
  returns (uint256)
{
  // If the provided funding cycle has no ballot, return the current timestamp.
  if (_fundingCycle.ballot == IJBFundingCycleBallot(address(0))) return block.timestamp;

  // Get a reference to the time the ballot ends.
  uint256 _ballotExpiration = _from + _fundingCycle.ballot.duration();

  // If the ballot ends in past, return the current timestamp. Otherwise return the ballot's expiration.
  return block.timestamp > _ballotExpiration ? block.timestamp : _ballotExpiration;
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
