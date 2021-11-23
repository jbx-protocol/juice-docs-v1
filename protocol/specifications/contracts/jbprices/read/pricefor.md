# priceFor

Contract:[`JBPrices`](../)​‌

Interface: `IJBPrices`

{% tabs %}
{% tab title="Step by step" %}
**Gets the current price of the provided currency in terms of the provided base currency.**

# Definition

```solidity
function priceFor(uint256 _currency, uint256 _base) external view override returns (uint256) { ... }
```

* Arguments:
  * `_currency` is the currency to get a price for.
  * `_base` is the currency to base the price on.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the `IJBPrices` interface.
* The function returns the price of the currency in terms of the base, with 18 decimals.

# Body

1.  Return 1 if the `_currency` and the `_base` are the same, since they have the same price. Normalize to `targetDecimals` number of decimals.

    ```solidity
    // If the currency is the base, return 1 since they are priced the same.
    if (_currency == _base) return 10**TARGET_DECIMALS;
    ```

    Internal references:

    * [`TARGET_DECIMALS`](../properties/targetdecimals.md)
2.  Get a reference to the feed.

    ```solidity
    // Get a reference to the feed.
    AggregatorV3Interface _feed = feedFor[_currency][_base];
    ```

    Internal references:

    * [`feedFor`](../properties/targetdecimals.md)
3.  Make sure there is a feed stored for the `_currency` `_base` pair.

    ```solidity
    // Feed must exist.
    require(_feed != AggregatorV3Interface(address(0)), '0x03: NOT_FOUND');
    ```
4.  Get the latest price being reported by the price feed. The `latestRoundData` function returns several feed parameters, but only the `_price` is needed.

    ```solidity
    // Get the latest round information. Only need the price is needed.
    (, int256 _price, , , ) = _feed.latestRoundData();
    ```
5.  Get the number of decimals being reported by the price feed that the `_price` is expected to have.

    ```solidity
    // Get a reference to the number of decimals the feed uses.
    uint256 _decimals = _feed.decimals();
    ```
6.  Return the `_price`, normalizing the value to `TARGET_DECIMALS` decimal fidelity.

    ```solidity
    // If decimals need adjusting, multiply or divide the price by the decimal adjuster to get the normalized result.
    if (TARGET_DECIMALS == _decimals) {
      return uint256(_price);
    } else if (TARGET_DECIMALS > _decimals) {
      return uint256(_price) * 10**(TARGET_DECIMALS - _decimals);
    } else {
      return uint256(_price) / 10**(_decimals - TARGET_DECIMALS);
    }
    ```

    Internal references:

    * [`TARGET_DECIMALS`](../properties/targetdecimals.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Gets the current price of the provided currency in terms of the provided base currency.
      
  @param _currency The currency to get a price for.
  @param _base The currency to base the price on.
      
  @return The price of the currency in terms of the base, with 18 decimals.
*/
function priceFor(uint256 _currency, uint256 _base) external view override returns (uint256) {
  // If the currency is the base, return 1 since they are priced the same.
  if (_currency == _base) return 10**TARGET_DECIMALS;

  // Get a reference to the feed.
  AggregatorV3Interface _feed = feedFor[_currency][_base];

  // Feed must exist.
  require(_feed != AggregatorV3Interface(address(0)), '0x03: NOT_FOUND');

  // Get the latest round information. Only need the price is needed.
  (, int256 _price, , , ) = _feed.latestRoundData();

  // Get a reference to the number of decimals the feed uses.
  uint256 _decimals = _feed.decimals();

  // If decimals need adjusting, multiply or divide the price by the decimal adjuster to get the normalized result.
  if (TARGET_DECIMALS == _decimals) {
    return uint256(_price);
  } else if (TARGET_DECIMALS > _decimals) {
    return uint256(_price) * 10**(TARGET_DECIMALS - _decimals);
  } else {
    return uint256(_price) / 10**(_decimals - TARGET_DECIMALS);
  }
 }
```
{% endtab %}

{% tab title="Errors" %}
| String                | Description                                                        |
| --------------------- | ------------------------------------------------------------------ |
| **`0x03: NOT_FOUND`** | Thrown if a feed wasn't found for the specified currency and base. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
