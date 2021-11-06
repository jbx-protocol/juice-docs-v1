# splitsStore

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../../../protocol/interfaces/ijbcontroller.md)

**The contract that stores splits for each project.**

## Definition

```solidity
/** 
  @notice 
  The contract that stores splits for each project.
*/
IJBSplitsStore public immutable splitsStore;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBController` interface.
