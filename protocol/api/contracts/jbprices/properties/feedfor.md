# feedFor

Contract: [`JBPrices`](../)

Interface: [`IJBPrices`](../../../interfaces/ijbprices.md)

**The available price feeds.**

# Definition

```solidity
/** 
  @notice 
  The available price feeds.

  @dev
  The feed returns the number of `_currency` units that can be converted to 1 `_base` unit.

  _currency The currency units the feed's resulting price is in terms of.
  _base The base currency unit being priced by the feed.
*/
mapping(uint256 => mapping(uint256 => IJBPriceFeed)) public override feedFor;
```

* Arguments:
  * `uint256` is the currency units the feed's resulting price is in terms of.
  * `uint256` is the base currency unit being priced by the feed.
* The resulting view function can be accessed externally by anyone.
* The resulting function overrides a function definition from the [`IJBPrices`](../../../interfaces/ijbprices.md) interface.
