# tokenStore

Contract: [`JBController`](broken-reference)​‌

Interface: [`IJBController`](../../../../../interfaces/ijbcontroller.md)

**The contract that manages token minting and burning.**

## Definition

```solidity
/** 
  @notice 
  The contract that manages token minting and burning.
*/
IJBTokenStore public immutable tokenStore;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBController` interface.
