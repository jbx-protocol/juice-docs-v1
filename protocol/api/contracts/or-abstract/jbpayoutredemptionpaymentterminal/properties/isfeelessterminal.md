# isFeelessTerminal

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

**Terminals that can be paid towards from this terminal without incurring a fee.**

# Definition

```solidity
/**
  @notice
  Terminals that can be paid towards from this terminal without incurring a fee.

  _terminal The terminal that can be paid toward.
*/
mapping(IJBPaymentTerminal => bool) public override isFeelessTerminal;
```

* Arguments:
  * `_terminal` is the terminal that can be paid toward.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
