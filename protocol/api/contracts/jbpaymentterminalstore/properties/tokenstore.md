# tokenStore

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)

**The contract that manages token minting and burning.**

# Definition

```solidity
/** 
  @notice 
  The contract that manages token minting and burning.
*/
IJBTokenStore public immutable override tokenStore;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md) interface.
