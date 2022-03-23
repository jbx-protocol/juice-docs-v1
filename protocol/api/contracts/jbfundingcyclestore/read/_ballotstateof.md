# _ballotStateOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**A project's latest funding cycle configuration approval status.**

### Definition

```solidity
function _ballotStateOf(
  uint256 _projectId,
  uint256 _configuration,
  uint256 _ballotFundingCycleConfiguration
) private view returns (JBBallotState) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funding cycle belongs.
  * `_configuration` is the funding cycle configuration to get the ballot state of.
  * `_ballotFundingCycleConfiguration` is the configuration of the funding cycle which is configured with the ballot that should be used.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns the [`JBBallotState`](../../../enums/jbballotstate.md) of the project.

### Body

1.  If there is no ballot configuration, the ballot state is implicitly approved.

    ```solidity
    // If there is no ballot funding cycle, implicitly approve.
    if (_ballotFundingCycleConfiguration == 0) return JBBallotState.Approved;
    ```
2.  Get the funding cycle that has a reference of the ballot that should be used.

    ```solidity
    // Get the ballot funding cycle.
    JBFundingCycle memory _ballotFundingCycle = _getStructFor(
      _projectId,
      _ballotFundingCycleConfiguration
    );
    ```

    _Internal references:_

    * [`_getStructFor`](./_getstructfor.md)
3.  If there's no ballot, the funding cycle configuration is implicitly approved. Otherwise if the ballot's duration has not yet expired, it is implicitly active. Otherwise, return the state that the ballot for the provided configuration.

    ```solidity
    // If there is no ballot, the ID is auto approved.
    // Otherwise if the ballot's duration hasn't passed, its still active.
    // Otherwise, return the ballot's state.
    if (_ballotFundingCycle.ballot == IJBFundingCycleBallot(address(0)))
      return JBBallotState.Approved;
    else if (_ballotFundingCycle.ballot.duration() >= block.timestamp - _configuration)
      return JBBallotState.Active;
    else return _ballotFundingCycle.ballot.stateOf(_projectId, _configuration);
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  A project's latest funding cycle configuration approval status.

  @param _projectId The ID of the project to which the funding cycle belongs.
  @param _configuration The funding cycle configuration to get the ballot state of.
  @param _ballotFundingCycleConfiguration The configuration of the funding cycle which is configured with the ballot that should be used.

  @return The ballot state of the project.
*/
function _ballotStateOf(
  uint256 _projectId,
  uint256 _configuration,
  uint256 _ballotFundingCycleConfiguration
) private view returns (JBBallotState) {
  // If there is no ballot funding cycle, implicitly approve.
  if (_ballotFundingCycleConfiguration == 0) return JBBallotState.Approved;

  // Get the ballot funding cycle.
  JBFundingCycle memory _ballotFundingCycle = _getStructFor(
    _projectId,
    _ballotFundingCycleConfiguration
  );

  // If there is no ballot, the ID is auto approved.
  // Otherwise if the ballot's duration hasn't passed, its still active.
  // Otherwise, return the ballot's state.
  if (_ballotFundingCycle.ballot == IJBFundingCycleBallot(address(0)))
    return JBBallotState.Approved;
  else if (_ballotFundingCycle.ballot.duration() >= block.timestamp - _configuration)
    return JBBallotState.Active;
  else return _ballotFundingCycle.ballot.stateOf(_projectId, _configuration);
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
