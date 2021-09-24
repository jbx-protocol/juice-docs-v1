# \_metadataOf

Contract:[`JBFundingCycleStore`](../)​‌

**Stores the metadata for each funding cycle, packed into one storage slot.**

Definition:

```javascript
/** 
  @notice
  Stores the metadata for each funding cycle, packed into one storage slot.
  
  [_projectId]
*/
mapping(uint256 => uint256) private _metadataOf;
```

* `uint256` is the the ID of the project to get the`_metadataOf`.
* The resulting view function is private to this contract.

