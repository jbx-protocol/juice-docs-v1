# controllerOf

Contract: [`JBDirectory`](../)​‌

Interface: [`IJBDirectory`](../../../interfaces/ijbdirectory.md)

**For each project ID, the controller that manages how terminals interact with tokens and funding cycles.**

# Definition

```solidity
/** 
  @notice 
  For each project ID, the controller that manages how terminals interact with tokens and funding cycles.

  _projectId The ID of the project to get the controller of.
*/
mapping(uint256 => IJBController) public override controllerOf;
```

* `_projectId` is the ID of the project to get the controller of.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBDirectory`](../../../interfaces/ijbdirectory.md) interface.
