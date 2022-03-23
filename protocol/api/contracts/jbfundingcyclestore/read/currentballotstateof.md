# currentBallotStateOf

Contract:[`JBFundingCycleStore`](../)​‌

Interface: [`IJBFundingCycleStore`](../../../interfaces/ijbfundingcyclestore.md)

{% tabs %}
{% tab title="Step by step" %}
**The current ballot state of the project.**

### Definition

```solidity
function currentBallotStateOf(uint256 _projectId) external view override returns (JBBallotState) { ... } 
```

* Arguments:
  * `_projectId` is the ID of the project to check the ballot state of.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBFundingCycleStore`](../../../interfaces/ijbfundingcyclestore.md) interface.
* The function returns the project's current [`JBBallotState`](../../../enums/jbballotstate.md).

### Body

1.  Get a reference to the latest funding cycle for the project.

    ```solidity
    // Get a reference to the latest funding cycle configuration.
    uint256 _fundingCycleConfiguration = latestConfigurationOf[_projectId];
    ```

    _Internal references:_

    * [`latestConfigurationOf`](../properties/latestconfigurationof.md)
2.  Get a reference to the funding cycle for the latest configuration.

    ```solidity
    // Resolve the funding cycle for the for the latest configuration.
    JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);
    ```

    _Internal references:_

    * [`_getStructFor`](_getstructfor.md)
3.  Return the ballot state of the latest funding cycle configuration as is determined by the current configuration and the funding cycle it's based on.

    ```solidity
    return _ballotStateOf(_projectId, _fundingCycle.configuration, _fundingCycle.basedOn);
    ```

    _Internal references:_

    * [`_ballotStateOf`](_ballotstateof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The current ballot state of the project.

  @param _projectId The ID of the project to check the ballot state of.

  @return The project's current ballot's state.
*/
function currentBallotStateOf(uint256 _projectId) external view override returns (JBBallotState) {
  // Get a reference to the latest funding cycle configuration.
  uint256 _fundingCycleConfiguration = latestConfigurationOf[_projectId];

  // Resolve the funding cycle for the for the latest configuration.
  JBFundingCycle memory _fundingCycle = _getStructFor(_projectId, _fundingCycleConfiguration);

  return _ballotStateOf(_projectId, _fundingCycle.configuration, _fundingCycle.basedOn);
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
