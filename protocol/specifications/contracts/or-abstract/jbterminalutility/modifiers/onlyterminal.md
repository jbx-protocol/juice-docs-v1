# onlyTerminal

```solidity
modifier onlyTerminal(uint256 _projectId) {
  require(directory.isTerminalDelegateOf(_projectId, msg.sender), '0x50: UNAUTHORIZED');
  _;
}
```