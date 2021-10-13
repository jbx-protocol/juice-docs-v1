# uriOf

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

**The IPFS CID for each project, which can be used to reference the project's metadata.**

_This is optional for each project._

Definition:

```solidity
/** 
  @notice 
  The IPFS CID for each project, which can be used to reference the project's metadata.

  @dev
  This is optional for each project.
*/
mapping(uint256 => string) public override uriOf;
```

* `uint256` is the ID of the project to which the URI belongs.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBProjects` interface.
