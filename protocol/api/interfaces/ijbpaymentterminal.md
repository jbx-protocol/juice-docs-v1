# IJBPaymentTerminal

```solidity
interface IJBPaymentTerminal {
  function token() external view returns (address);

  function decimals() external view returns (uint256);

  function currency() external view returns (uint256);

  function baseWeightCurrency() external view returns (uint256);

  function payoutSplitsGroup() external view returns (uint256);

  // Return value must be a fixed point number with 18 decimals.
  function currentEthOverflowOf(uint256 _projectId) external view returns (uint256);

  function pay(
    uint256 _amount,
    uint256 _projectId,
    address _beneficiary,
    uint256 _minReturnedTokens,
    bool _preferClaimedTokens,
    string calldata _memo,
    bytes calldata _metadata
  ) external payable;

  function addToBalanceOf(
    uint256 _projectId,
    uint256 _amount,
    string calldata _memo
  ) external payable;
}
```
