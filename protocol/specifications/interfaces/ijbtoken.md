# IJBToken

```solidity
interface IJBToken {
  function totalSupply(uint256 _projectId) external view returns (uint256);

  function balanceOf(address _account, uint256 _projectId) external view returns (uint256);

  function mint(
    uint256 _projectId,
    address _account,
    uint256 _amount
  ) external;

  function burn(
    uint256 _projectId,
    address _account,
    uint256 _amount
  ) external;

  function approve(
    uint256,
    address _spender,
    uint256 _amount
  ) external;

  function transfer(
    uint256 _projectId,
    address _to,
    uint256 _amount
  ) external;

  function transferFrom(
    uint256 _projectId,
    address _from,
    address _to,
    uint256 _amount
  ) external;

  function transferOwnership(address _newOwner) external;
}
```
