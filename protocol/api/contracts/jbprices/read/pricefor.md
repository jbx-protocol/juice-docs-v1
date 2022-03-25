# priceFor

Contract:[`JBPrices`](../)​‌

Interface: [`IJBPrices`](../../../interfaces/ijbprices.md)

{% tabs %}
{% tab title="Step by step" %}
**Gets the number of `_currency` units that can be converted to 1 `_base` unit.**

### Definition

```solidity
function priceFor(
  uint256 _currency,
  uint256 _base,
  uint256 _decimals
) external view override returns (uint256) { ... }
```

* Arguments:
  * `_currency` is the currency units the feed's resulting price is in terms of.
  * `_base` is the base currency unit being priced by the feed.
  * `_decimals` is the number of decimals the returned fixed point price should include.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBPrices`](../../../interfaces/ijbprices.md) interface.
* The function returns the price of the currency in terms of the base, as a fixed point number with the specified number of decimals.

### Body

1.  Return 1 if the currency and the base are the same, since they have the same price. Normalize to the desired number of decimals.

    ```solidity
    // If the currency is the base, return 1 since they are priced the same. Include the desired number of decimals.
    if (_currency == _base) return 10**_decimals;
    ```
2.  Get a reference to the feed.

    ```solidity
    // Get a reference to the feed.
    IJBPriceFeed _feed = feedFor[_currency][_base];
    ```

    Internal references:

    * [`feedFor`](../properties/feedfor.md)
3.  Make sure there is a feed stored for the currency base pair.

    ```solidity
    // Feed must exist.
    if (_feed == IJBPriceFeed(address(0))) revert PRICE_FEED_NOT_FOUND();
    ```
4.  Return the latest price being reported by the price feed. 

    ```solidity
    // Get the price.
    return _feed.getPrice(_decimals);
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Gets the number of `_currency` units that can be converted to 1 `_base` unit.
  
  @param _currency The currency units the feed's resulting price is in terms of.
  @param _base The base currency unit being priced by the feed.
  @param _decimals The number of decimals the returned fixed point price should include.
  
  @return The price of the currency in terms of the base, as a fixed point number with the specified number of decimals.
*/
function priceFor(
  uint256 _currency,
  uint256 _base,
  uint256 _decimals
) external view override returns (uint256) {
  // If the currency is the base, return 1 since they are priced the same. Include the desired number of decimals.
  if (_currency == _base) return 10**_decimals;

  // Get a reference to the feed.
  IJBPriceFeed _feed = feedFor[_currency][_base];

  // Feed must exist.
  if (_feed == IJBPriceFeed(address(0))) revert PRICE_FEED_NOT_FOUND();

  // Get the price.
  return _feed.getPrice(_decimals);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                     | Description                                                        |
| -------------------------- | ------------------------------------------------------------------ |
| **`PRICE_FEED_NOT_FOUND`** | Thrown if a feed wasn't found for the specified currency and base. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
