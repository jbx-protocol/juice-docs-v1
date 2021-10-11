# feedFor

Contract: [`JBPrices`](../)

Interface:** **`IJBPrices`

**The available price feeds.**\
****\
****Definition:

```solidity
/** 
  @notice 
  The available price feeds.

  [_currency][_base]
*/
mapping(uint256 => mapping(uint256 => AggregatorV3Interface)) public override feedFor;
```

* `uint256` is the currency of the feed.
* `uint256` is the base of the feed. 
* The resulting view function can be accessed externally by anyone. 
* The resulting function overrides a function definition from the `IJBPrices` interface.
