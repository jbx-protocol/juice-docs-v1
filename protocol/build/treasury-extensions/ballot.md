# Ballot

Before implementing, learn about ballots [here](../../learn/glossary/ballot.md).
### Specs

A contract can become a funding cycle ballot by adhering to [`IJBFundingCycleBallot`](../../api/interfaces/ijbfundingcycleballot.md):

```solidity
interface IJBFundingCycleBallot {
  function duration() external view returns (uint256);

  function stateOf(uint256 _projectId, uint256 _configuration)
    external
    view
    returns (JBBallotState);
}
```

There are two functions that must be implemented, `duration(...)` and `stateOf(...)`. The result of `duration(...)` is the number of seconds the ballot lasts for from the moment the reconfiguration is proposed. During this time, the protocol automatically interprets the ballot's state as [`JBBallotState.ACTIVE`](../../api/enums/jbballotstate.md). The result of `stateOf(...)` returns the [`JBBallotState`](../../api/enums/jbballotstate.md). If a configuration is approved and the duration has expired, the [`JBFundingCycleStore`](../../api/contracts/jbfundingcyclestore/) will use it as the project's current funding cycle when it becomes active. Otherwise, it will make a copy of the latest approved cycle to use.

When extending the pay functionality with a delegate, the protocol will pass a `projectId` and a `configuration` to the `stateOf(...)` function. `configuration` is the identifier of the funding cycle being evaluated, and also the unix timestamp in seconds when the reconfiguration was proposed.

Once the `duration(...)` has expired, the returned value of `stateOf(...)` should no longer change. 

### Attaching

A ballot contract should be deployed independently. Once deployed, its address can be configured into a project's funding cycle to take effect while that funding cycle is active for evaluating subsequent reconfigurations. 