# _MAX_FIXED_POINT_FIDELITY

Contract:[`JBPaymentTerminalStore`](../)​‌

**Ensures a maximum number of decimal points of persisted fidelity on mulDiv operations of fixed point numbers.** 

# Definition

```solidity
/**
  @notice
  Ensures a maximum number of decimal points of persisted fidelity on mulDiv operations of fixed point numbers. 
*/
uint256 private constant _MAX_FIXED_POINT_FIDELITY = 18;
```

* This value must be hardcoded.
* The resulting view function is private to this contract.