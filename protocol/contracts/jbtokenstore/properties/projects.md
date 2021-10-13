# projects

Contract:[`JBTokenStore`](../)​‌

Interface: `IJBTokenStore`

**The Projects contract which mints ERC-721's that represent project ownership and transfers.**

Definition:

```solidity
/** 
  @notice 
  The Projects contract which mints ERC-721's that represent project ownership and transfers.
*/ 
IJBProjects public immutable override projects;
```

* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBTokenStore` interface.

