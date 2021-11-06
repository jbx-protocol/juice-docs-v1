# onlyController

```solidity
modifier onlyController(uint256 _projectId) {
  require(address(directory.controllerOf(_projectId)) == msg.sender, '0x4f: UNAUTHORIZED');
  _;
}
```