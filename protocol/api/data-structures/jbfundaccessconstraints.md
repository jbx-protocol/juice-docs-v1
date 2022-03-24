# JBOverflowAllowance

```solidity
struct JBFundAccessConstraints {
  // The terminal within which the distribution limit and the overflow allowance applies.
  IJBPaymentTerminal terminal;
  // The amount of the distribution limit, as a fixed point number with the same number of decimals as the terminal within which the limit applies.
  uint256 distributionLimit;
  // The currency of the distribution limit.
  uint256 distributionLimitCurrency;
  // The amount of the allowance, as a fixed point number with the same number of decimals as the terminal within which the allowance applies.
  uint256 overflowAllowance;
  // The currency of the overflow allowance.
  uint256 overflowAllowanceCurrency;
}
```
