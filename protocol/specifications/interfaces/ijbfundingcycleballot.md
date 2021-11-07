# IJBFundingCycleBallot

```solidity
interface IJBFundingCycleBallot {
  function duration() external view returns (uint256);

  function stateOf(uint256 _fundingCycleId, uint256 _configured)
    external
    view
    returns (JBBallotState);
}
```
