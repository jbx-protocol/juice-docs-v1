# \_primaryTerminalOf

Contract:[`JBDirectory`](../)‌

**The Projects contract which mints ERC-721's that represent project ownership and transfers.**

# Definition

```solidity
/** 
  @notice 
  The project's primary terminal for a token.

  [_projectId][_token]
*/
mapping(uint256 => mapping(address => IJBTerminal)) private _primaryTerminalOf;
```

* `_projectId` is the ID of the project to get the primary terminal of.
* `_token` is the token to get the project's primary terminal of.
* The resulting view function is private to this contract.
