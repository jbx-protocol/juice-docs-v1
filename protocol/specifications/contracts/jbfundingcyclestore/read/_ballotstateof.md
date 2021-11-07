# \_ballotStateOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**A funding cycle configuration's current status.**

## Definition

```solidity
function _ballotStateOf(
  uint256 _id,
  uint256 _configuration,
  uint256 _ballotFundingCycleId
) private view returns (JBBallotState) { ... }
```

* Arguments:
  * `_id` is the ID of the funding cycle configuration to check the status of.
  * `_configuration` is the timestamp when the configuration took place. This differentiates reconfigurations onto the same upcoming funding cycle, which all would have the same ID but different configuration times.
  * `_ballotFundingCycleId` is the ID of the funding cycle which is configured with the ballot that should be used.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the [`JBBallotState`](../../../enums/jbballotstate.md).

## Body

1.  If there is no ballot, the ballot state is implicitly approved.

    ```solidity
    // If there is no ballot funding cycle, implicitly approve.
    if (_ballotFundingCycleId == 0) return JBBallotState.Approved;
    ```
2.  Get the `JBFundingCycle` struct that has a reference of the ballot that should be used.

    ```solidity
    // Get the ballot funding cycle.
    JBFundingCycle memory _ballotFundingCycle = _getStructFor(_ballotFundingCycleId);
    ```
3.  If there's no ballot, implicitly the funding cycle configuration is implicitly approved. Otherwise, return the state that the ballot for the provided `_id` and `_configuration`.

    ```solidity
    // If there is no ballot, the ID is auto approved.
    // Otherwise, return the ballot's state.
    return
      _ballotFundingCycle.ballot == IJBFundingCycleBallot(address(0))
        ? JBBallotState.Approved
        : _ballotFundingCycle.ballot.stateOf(_id, _configuration);
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  A funding cycle configuration's current status.

  @param _id The ID of the funding cycle configuration to check the status of.
  @param _configuration This differentiates reconfigurations onto the same upcoming funding cycle, which all would have the same ID but different configuration times.
  @param _ballotFundingCycleId The ID of the funding cycle which is configured with the ballot that should be used.

  @return The funding cycle's configuration status.
*/
function _ballotStateOf(
  uint256 _id,
  uint256 _configuration,
  uint256 _ballotFundingCycleId
) private view returns (JBBallotState) {
  // If there is no ballot funding cycle, implicitly approve.
  if (_ballotFundingCycleId == 0) return JBBallotState.Approved;

  // Get the ballot funding cycle.
  JBFundingCycle memory _ballotFundingCycle = _getStructFor(_ballotFundingCycleId);

  // If there is no ballot, the ID is auto approved.
  // Otherwise, return the ballot's state.
  return
    _ballotFundingCycle.ballot == IJBFundingCycleBallot(address(0))
      ? JBBallotState.Approved
      : _ballotFundingCycle.ballot.stateOf(_id, _configuration);
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
