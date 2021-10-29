# IJBVault

```solidity
interface IJBVault {
  event Deposit(uint256 indexed projectId, uint256 amount, address caller);
  event Withdraw(uint256 indexed projectId, uint256 amount, address to, address caller);
  
  function token() external view returns (address);

  function deposit(uint256 _projectId, uint256 _amount) external payable;

  function withdraw(
    uint256 _projectId,
    uint256 _amount,
    address payable _to
  ) external;
}
```
