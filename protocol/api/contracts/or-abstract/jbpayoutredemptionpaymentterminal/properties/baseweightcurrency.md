# decimals

Contract: [`JBPayoutRedemptionPaymentTerminal`](../)​‌

Interface: [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md)

**The currency to base token issuance on.**

_If this differs from `currency`, there must be a price feed available to convert `currency` to `baseWeightCurrency`._

# Definition

```solidity
/**
  @notice
  The currency to base token issuance on.

  @dev
  If this differs from `currency`, there must be a price feed available to convert `currency` to `baseWeightCurrency`.
*/
uint256 public immutable override baseWeightCurrency;
```

* The value cannot be changed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBPayoutRedemptionPaymentTerminal`](../../../../interfaces/ijbpayoutredemptionpaymentterminal.md) interface.
