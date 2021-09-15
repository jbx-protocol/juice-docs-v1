# priceFor

Contract:[`JBPrices`](../)​‌

Interface: `IJBPrices`

{% tabs %}
{% tab title="Step by step" %}
**Gets the current price of the provided currency in terms of the provided base currency.**  
  
Definition:

```javascript
function priceFor(uint256 _currency, uint256 _base)
    external
    view
    override
    returns (uint256) { ... }
```

* The function can be accessed externally by anyone. 
* The function overrides a function definition from the `IJBPrices` interface.
* The function returns the price of the currency in terms of the base, with 18 decimals.

1. Return 1 if the `_currency` and the `_base` are the same, since they have the same price. Normalize to `targetDecimals` number of decimals.  


   Internal references:

   * [`targetDecimals`](../properties/targetdecimals.md)

   ```javascript
   // If the currency is the base, return 1 since they are priced the same.
   if (_currency == _base) return 10**targetDecimals;
   ```

2. Get a reference to the feed.  


   Internal references:

   * [`feedFor`](../properties/targetdecimals.md)

   ```javascript
   // Get a reference to the feed.
   AggregatorV3Interface _feed = feedFor[_currency][_base];
   ```

3. Make sure there is a feed stored for the `_currency` `_base` pair.

   ```javascript
   // Feed must exist.
   require(
       _feed != AggregatorV3Interface(address(0)),
       "JBPrices::priceFor: NOT_FOUND"
   );
   ```

4. Get the latest price being reported by the price feed. The `latestRoundData` function returns several feed parameters, but only the `_price` is needed.

   ```javascript
   // Get the latest round information. Only need the price is needed.
   (, int256 _price, , , ) = _feed.latestRoundData();
   ```

5. Return the `_price`, normalizing the value to `targetDecimals` decimal fidelity.  


   Internal references:

   * [`targetDecimals`](../properties/targetdecimals.md)

   ```javascript
   // Multiply the price by the decimal adjuster to get the normalized result.
   return uint256(_price) * feedDecimalAdjuster[_currency][_base]
   ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Gets the current price of the provided currency in terms of the provided base currency.

  @param _currency The currency to get a price for.
  @param _base The currency to base the price on.

  @return The price of the currency in terms of the base, with 18 decimals.
*/
function priceFor(uint256 _currency, uint256 _base)
    external
    view
    override
    returns (uint256)
{
    // If the currency is the base, return 1 since they are priced the same.
    if (_currency == _base) return 10**targetDecimals;

    // Get a reference to the feed.
    AggregatorV3Interface _feed = feedFor[_currency][_base];

    // Feed must exist.
    require(
        _feed != AggregatorV3Interface(address(0)),
        "JBPrices::priceFor: NOT_FOUND"
    );

    // Get the latest round information. Only need the price is needed.
    (, int256 _price, , , ) = _feed.latestRoundData();

    // Multiply the price by the decimal adjuster to get the normalized result.
    return uint256(_price) * feedDecimalAdjuster[_currency][_base];
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBPrices::priceFor: NOT_FOUND`** | Thrown if a feed wasn't found for the specified currency and base. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}

