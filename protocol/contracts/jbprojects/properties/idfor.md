# idFor

Contract: [`JBProjects`](../)

Interface:`IJBProjects`

**The ID of the project that each unique handle is currently referencing.**

Definition:

```solidity
/** 
  @notice 
  The ID of the project that each unique handle is currently referencing.
*/
mapping(bytes32 => uint256) public override idFor;
```

* `bytes32` is the handle from which the project ID can be referenced.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBProjects` interface.
