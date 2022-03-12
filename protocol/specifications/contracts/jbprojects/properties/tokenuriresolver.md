# tokenUriResolver

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

**The contract resolving each project ID to its ERC721 URI.**

# Definition

```solidity
/**
  @notice
  The contract resolving each project ID to its ERC721 URI.
*/
IJBTokenUriResolver public override tokenUriResolver;
```

* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBProjects`](../../../interfaces/ijbprojects.md) interface.