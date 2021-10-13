# feedDecimalAdjusterFor

Contract: [`JBPrices`](../)

Interface:** **`IJBPrices`

**The number to multiply each price feed by to get to the target decimals.**

Definition:

```solidity
/** 
  @notice 
  The number to multiply each price feed by to get to the target decimals.

  [_currency][_base]
*/
mapping(uint256 => mapping(uint256 => uint256)) public override feedDecimalAdjusterFor;
```

* `uint256` is the currency of the feed to get the decimal adjuster for.
* `uint256` is the base of the feed to get the decimal adjuster for. 
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBPrices` interface.
