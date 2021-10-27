# feedDecimalAdjusterFor

Contract: [`JBPrices`](../)

Interface: [`IJBPrices`](../../../interfaces/ijbprices.md)

**The number to multiply each price feed by to get to the target decimals.**

## Definition

```solidity
/** 
  @notice 
  The number to multiply each price feed by to get to the target decimals.

  _currency The currency of the feed to get the decimal adjuster for.
  _base The base of the feed to get the decimal adjuster for. 
*/
mapping(uint256 => mapping(uint256 => uint256)) public override feedDecimalAdjusterFor;
```

* `_currency` is the currency of the feed to get the decimal adjuster for.
* `_base` is the base of the feed to get the decimal adjuster for. 
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBPrices` interface.
