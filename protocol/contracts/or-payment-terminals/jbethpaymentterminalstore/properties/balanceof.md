# balanceOf

Contract: [`JBETHPaymentTerminalStore`](broken-reference)​‌

**The amount of ETH that each project has.**

## Definition

```solidity
/** 
  @notice 
  The amount of ETH that each project has.

  _projectId The ID of the project to get the balance of.
*/
mapping(uint256 => uint256) public balanceOf;
```

* `_projectId` is the ID of the project to get the balance of.
* The resulting view function can be accessed externally by anyone.
