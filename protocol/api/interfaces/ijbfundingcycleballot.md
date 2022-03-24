# IJBFundingCycleBallot

```solidity
interface IJBFundingCycleBallot {
  function duration() external view returns (uint256);

  function stateOf(uint256 _projectId, uint256 _configuration)
    external
    view
    returns (JBBallotState);
}
```
