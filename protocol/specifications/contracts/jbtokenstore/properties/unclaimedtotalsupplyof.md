# unclaimedTotalSupplyOf

Contract: [`JBTokenStore`](broken-reference)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

**The total supply of unclaimed tokens for each project.**

## Definition

```solidity
/** 
  @notice
  The total supply of unclaimed tokens for each project.

  _projectId The ID of the project to which the token belongs.
*/
mapping(uint256 => uint256) public override unclaimedTotalSupplyOf;
```

* `_projectId` is the ID of the project to which the token belongs.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBTokenStore` interface.
