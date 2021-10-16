# IJBController

```solidity
interface IJBController {
  function reservedTokenBalanceOf(uint256 _projectId, uint256 _reservedRate)
    external
    view
    returns (uint256);

  function prepForMigration(uint256 _projectId) external;

  function mintTokensOf(
    uint256 _projectId,
    uint256 _tokenCount,
    address _beneficiary,
    string calldata _memo,
    bool _preferClaimedTokens,
    bool _shouldReserveTokens
  ) external;

  function burnTokensOf(
    address _holder,
    uint256 _projectId,
    uint256 _tokenCount,
    string calldata _memo,
    bool _preferClaimedTokens
  ) external;

  function signalWithdrawlFrom(uint256 _projectId, uint256 _amount)
    external
    returns (JBFundingCycle memory);

  function overflowAllowanceOf(
    uint256 _projectId,
    uint256 _configuration,
    IJBTerminal _terminal
  ) external view returns (uint256);
}sol
```
