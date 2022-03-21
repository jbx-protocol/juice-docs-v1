# IJBController

```solidity
interface IJBController {
  function reservedTokenBalanceOf(uint256 _projectId, uint256 _reservedRate)
    external
    view
    returns (uint256);

  function prepForMigrationOf(uint256 _projectId, IJBController _from) external;

  function mintTokensOf(
    uint256 _projectId,
    uint256 _tokenCount,
    address _beneficiary,
    string calldata _memo,
    bool _preferClaimedTokens,
    uint256 _reserveRate
  ) external returns (uint256 beneficiaryTokenCount);

  function burnTokensOf(
    address _holder,
    uint256 _projectId,
    uint256 _tokenCount,
    string calldata _memo,
    bool _preferClaimedTokens
  ) external;

  function overflowAllowanceOf(
    uint256 _projectId,
    uint256 _configuration,
    IJBPaymentTerminal _terminal
  ) external view returns (uint256);

  function overflowAllowanceCurrencyOf(
    uint256 _projectId,
    uint256 _configuration,
    IJBPaymentTerminal _terminal
  ) external view returns (uint256);

  function distributionLimitOf(
    uint256 _projectId,
    uint256 _configuration,
    IJBPaymentTerminal _terminal
  ) external view returns (uint256);

  function distributionLimitCurrencyOf(
    uint256 _projectId,
    uint256 _configuration,
    IJBPaymentTerminal _terminal
  ) external view returns (uint256);
}
```
