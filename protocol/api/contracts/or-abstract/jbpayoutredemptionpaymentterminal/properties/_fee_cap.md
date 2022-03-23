# \_MAX\_FEE

Contract:[`JBPayoutRedemptionPaymentTerminal`](../)​‌

**Maximum fee that can be set for a funding cycle configuration.** 

_Out of MAX\_FEE (50\_000\_000 / 1\_000\_000\_000)_

# Definition

```solidity
/**
  @notice
  Maximum fee that can be set for a funding cycle configuration.

  @dev
  Out of MAX_FEE (50_000_000 / 1_000_000_000)
*/
uint256 private constant _FEE_CAP = 50_000_000;
```

* This value must be hardcoded.
* The resulting view function is private to this contract.