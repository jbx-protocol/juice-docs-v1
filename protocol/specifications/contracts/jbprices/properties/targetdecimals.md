# TARGET\_DECIMALS

Contract: [`JBPrices`](../)

Interface: [`IJBPrices`](../../../interfaces/ijbprices.md)

**The normalized number of decimals each price feed has.**

# Definition

```solidity
/** 
  @notice 
  The normalized number of decimals each price feed has.
*/
uint256 public constant override TARGET_DECIMALS = 18;
```

* This value is hardcoded.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBPrices` interface.
