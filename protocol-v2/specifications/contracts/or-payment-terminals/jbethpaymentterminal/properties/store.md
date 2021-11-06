# store

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBETHPaymentTerminal`](../../../../../../protocol/interfaces/ijbethterminalof.md)

**The contract that stores and manages the terminal's data.**

## Definition

```solidity
/**
  @notice 
  The contract that stores and manages the terminal's data.
*/
JBETHPaymentTerminalStore public immutable store;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBETHPaymentTerminal` interface.
