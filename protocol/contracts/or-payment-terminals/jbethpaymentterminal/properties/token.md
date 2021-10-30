# token

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBTerminal`](../../../../interfaces/ijberminal.md)

**The token that this terminal accepts.**

_ETH is represented as address 0x0000000000000000000000000000000000042069._

## Definition

```solidity
/** 
  @notice 
  The token that this terminal accepts. 

  @dev
  ETH is represented as address 0x0000000000000000000000000000000000042069.
*/
address public immutable override token = 0x0000000000000000000000000000000000042069;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBTerminal` interface.
