# \_tappedAmountOf

Contract:[`JBFundingCycleStore`](../)​

**Stores the amount that has been tapped within each funding cycle.**

Definition:

```javascript
/** 
  @notice
  Stores the amount that has been tapped within each funding cycle.
  
  [_projectId]
*/
mapping(uint256 => uint256) private _tappedAmountOf;
```

* `uint256` is the ID of the project to get the `_tappedAmountOf`.
* The resulting view function is private to this contract.



