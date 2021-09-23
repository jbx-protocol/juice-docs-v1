# \_packedConfigurationPropertiesOf

Contract:[`JBFundingCycleStore`](../)​‌

**Stores the reconfiguration properties of each funding cycle, packed into one storage slot.**

Definition:

```javascript
/** 
  @notice
  Stores the reconfiguration properties of each funding cycle, packed into one storage slot.
*/
mapping(uint256 => uint256) private _packedConfigurationPropertiesOf;
```

* The resulting view function is private to this contract.

