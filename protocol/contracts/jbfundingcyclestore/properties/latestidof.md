# latestIdOf

Contract:[`JBFundingCycleStore`](../)​‌

**The ID of the latest stored funding cycle for each project.**

```javascript
/** 
  @notice 
  The ID of the latest stored funding cycle for each project.
*/
mapping(uint256 => uint256) public override latestIdOf;
```

* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBFundingCycleStore` interface.

