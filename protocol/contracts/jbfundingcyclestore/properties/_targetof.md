# \_targetOf

Contract:[`JBFundingCycleStore`](../)​‌

**Stores the amount that each funding cycle can tap during the funding cycle.**

Definition:

```javascript
/** 
  @notice
  Stores the amount that each funding cycle can tap during the funding cycle.
  
  [_projectId]
*/
mapping(uint256 => uint256) private _targetOf;
```

* `uint256` is the ID of the project to get the `_targetOf`.
* The resulting view function is private to this contract.

