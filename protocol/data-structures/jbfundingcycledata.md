# JBFundingCycleData

```javascript
struct JBFundingCycleData {
  uint256 target;
  uint256 currency;
  uint256 duration;
  uint256 cycleLimit;
  uint256 discountRate;
  // Send a weight of 1 to set a minimum.
  uint256 weight;
  IJBFundingCycleBallot ballot;
}
```

