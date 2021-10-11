# tokenOf

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

**Each project's ERC20 Token tokens.**

Definition:

```solidity
/** 
  @notice
  Each project's ERC20 Token tokens.

  [_projectId]
*/
mapping(uint256 => IJBToken) public override tokenOf;
```

* `uint256` is the ID of the project to which the token belongs.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBTokenStore` interface.

