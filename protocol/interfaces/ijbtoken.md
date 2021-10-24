# IJBToken

```solidity
interface IJBToken is IERC20 {
  function mint(address _account, uint256 _amount) external;

  function burn(address _account, uint256 _amount) external;

  function transferOwnership(address newOwner) external;
}
```
