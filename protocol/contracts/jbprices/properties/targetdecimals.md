# targetDecimals

Contract: [`JBPrices`](../)

Interface: ****`IJBPrices`

**The normalized number of decimals each price feed has.**  
  
Definition:

```javascript
/** 
  @notice 
  The normalized number of decimals each price feed has.
*/
uint256 public constant override targetDecimals = 18;
```

* This value cannot change.
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBPrices` interface.
