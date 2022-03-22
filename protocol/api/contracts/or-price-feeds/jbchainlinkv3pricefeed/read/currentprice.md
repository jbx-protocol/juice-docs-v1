# currentPrice

Contract:[`JBChainlinkV3PriceFeed`](../)​‌

Interface: [`IJBPriceFeed`](../../../../interfaces/ijbpricefeed.md)

{% tabs %}
{% tab title="Step by step" %}
**Gets the current price from the feed, normalized to the specified number of decimals.**

### Definition

```solidity
function currentPrice(uint256 _decimals) external view override returns (uint256)  { ... }
```

* Arguments:
  * `_decimals` is the number of decimals the returned fixed point price should include.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBPriceFeed`](../../../../interfaces/ijbpricefeed.md) interface.
* The function returns the current price of the feed, as a fixed point number with the specified number of decimals.

### Body

1.  Get the latest price being reported by the price feed. The `latestRoundData` function returns several feed parameters, but only the `_price` is needed.

    ```solidity
    // Get the latest round information. Only need the price is needed.
    (, int256 _price, , , ) = feed.latestRoundData();
    ```

    Internal references:

    * [`feed`](../properties/feed.md)

    External references:

    * [`latestRoundData`](https://docs.chain.link/docs/price-feeds-api-reference/#latestrounddata)
2.  Get the number of decimals being reported by the price feed that the provided price is expected to have.

    ```solidity
    // Get a reference to the number of decimals the feed uses.
    uint256 _feedDecimals = feed.decimals();
    ```

    External references:

    * [`decimals`](https://docs.chain.link/docs/price-feeds-api-reference/#decimals)
3. Return the fixed point price after normalizing the value to the desired number of decimals.

    ```solidity
    // Return the price, adjusted to the target decimals.
    return uint256(_price).adjustDecimals(_feedDecimals, _decimals);
    ```

    _Libraries used:_

    * [`JBFixedPointNumber`](../../../../libraries/jbfixedpointnumber.md)
      * `.adjustDecimals(...)`
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Gets the current price from the feed, normalized to the specified number of decimals.

  @param _decimals The number of decimals the returned fixed point price should include.

  @return The current price of the feed, as a fixed point number with the specified number of decimals.
*/
function currentPrice(uint256 _decimals) external view override returns (uint256) {
  // Get the latest round information. Only need the price is needed.
  (, int256 _price, , , ) = feed.latestRoundData();

  // Get a reference to the number of decimals the feed uses.
  uint256 _feedDecimals = feed.decimals();

  // Return the price, adjusted to the target decimals.
  return uint256(_price).adjustDecimals(_feedDecimals, _decimals);
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
