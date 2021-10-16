# unclaimedTotalSupplyOf

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

**Each holder's balance of unclaimed Tokens for each project.**

# Definition

```solidity
/** 
  @notice
  Each holder's balance of unclaimed Tokens for each project.

  [_holder][_projectId]
*/
mapping(address => mapping(uint256 => uint256)) public override unclaimedBalanceOf;
```

* `address` is the holder of balance.
* `uint256` is the ID of the project to which the token belongs.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBTokenStore` interface.

