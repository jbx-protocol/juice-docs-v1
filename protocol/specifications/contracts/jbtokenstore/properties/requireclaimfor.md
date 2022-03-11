# requireClaimFor

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

**A flag indicating if tokens are required to be issued as claimed for a particular project.**

# Definition

```solidity
/**
  @notice
  A flag indicating if tokens are required to be issued as claimed for a particular project.

  _projectId The ID of the project to which the requirement applies.
*/
mapping(uint256 => bool) public override requireClaimFor;
```

* `_projectId` is the ID of the project to which the requirement applies.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) interface.
