# feedFor

Contract: [`JBPrices`](../)

Interface: [`IJBPrices`](../../../../interfaces/ijbprices.md)

**The available price feeds.**

## Definition

```solidity
/** 
  @notice 
  The available price feeds.

  _currency he currency of the feed.
  _base he base of the feed. 
*/
mapping(uint256 => mapping(uint256 => AggregatorV3Interface)) public override feedFor;
```

* `uint256` is the currency of the feed.
* `uint256` is the base of the feed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the `IJBPrices` interface.
