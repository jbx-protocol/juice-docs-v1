# decimals

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

**The group that payout splits coming from this terminal are identified by.**

# Definition

```solidity
/**
  @notice
  The group that payout splits coming from this terminal are identified by.
*/
uint256 public immutable override payoutSplitsGroup;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
