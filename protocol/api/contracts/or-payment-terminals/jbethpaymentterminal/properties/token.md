# token

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md)

**The token that this terminal accepts.**
# Definition

```solidity
/** 
  @notice 
  The token that this terminal accepts. 
*/
address public immutable override token = JBTokens.ETH;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBPaymentTerminal` interface.
