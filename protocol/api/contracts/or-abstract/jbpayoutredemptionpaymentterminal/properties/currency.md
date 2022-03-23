# decimals

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md)

**The currency to use when resolving price feeds for this terminal.**

# Definition

```solidity
/**
  @notice
  The currency to use when resolving price feeds for this terminal.
*/
uint256 public immutable override currency;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md) interface.
