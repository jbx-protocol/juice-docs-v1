# addFeedFor

Contract:[`JBPrices`](../)​‌

Interface: `IJBPrices`

{% tabs %}
{% tab title="Step by step" %}
**Add an ETH price feed for a currency.**

_Current feeds can't be modified._  
  
Definition:

```javascript
function addFeedFor(uint256 _currency, uint256 _base, AggregatorV3Interface _feed)
    external
    override
    onlyOwner { ... }
```

* `_currency` is the currency that the price feed is for.
* `_base` is the currency that the price feed is based on.
* `_feed` is the price feed being added.
* The function can only be accessed by the address that owns this contract.
* The function overrides a function definition from the `IJBPrices` interface.
* The function doesn't return anything.

1. Make sure there isn't already a price feed set for the `_currency` `_base` pair.  


   Internal references:

   * [`feedFor`](../properties/feedfor.md)

   ```javascript
   // There can't already be a feed for the specified currency.
   require(feedFor[_currency][_base] == AggregatorV3Interface(address(0)),
       "Prices::addFeedFor: ALREADY_EXISTS"
   );
   ```

2. Get a reference to how many decimal places the provided price feed uses in the quoted rates. 

   ```javascript
   // Get a reference to the number of decimals the feed uses.
   uint256 _decimals = _feed.decimals();
   ```

3. Make sure the feed doesn't use more decimals than what the contract expects. This contract isn't design to support feeds that use more than 18 decimals of fidelity.  


   Internal references:

   * [`targetDecimals`](../properties/targetdecimals.md)

   ```javascript
   // Decimals should be less than or equal to the target number of decimals.
   require(
       _decimals <= targetDecimals,
       "Prices::addFeedFor: BAD_DECIMALS"
   );
   ```

4. Store the provided feed for the `_currency` `_base` pair.

   ```javascript
   // Set the feed.
   feedFor[_currency][_base] = _feed;
   ```

5. Store a value that price feed results will be multiplied by in order to always be reported with `targetDecimals` fidelity. The prices from this contract are always reported with `targetDecimals` fidelity – if the provided feed reports with fewer decimals, the contract must know how to adjust the price feed to normalize results.  


   Internal references:

   * [`feedDecimalAdjusterFor`](../properties/feeddecimaladjuster.md)
   * [`targetDecimals`](../properties/targetdecimals.md)

   ```javascript
   // Set the decimal adjuster for the currency.
   feedDecimalAdjusterFor[_currency][_base] = 10**(targetDecimals - _decimals);
   ```

6. Emit an `AddFeed` event with the all relevant parameters.   


   _Event references:_

   * [`AddFeed`](../events/addfeed.md)

   ```javascript
   emit AddFeed(_currency, _base, _decimals, _feed);
   ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
   Add a price feed for a currency in terms of the provided base currency.

  @dev
  Current feeds can't be modified.

  @param _currency The currency that the price feed is for.
  @param _base The currency that the price feed is based on.
  @param _feed The price feed being added.
*/
function addFeedFor(uint256 _currency, uint256 _base, AggregatorV3Interface _feed)
    external
    override
    onlyOwner
{
    // There can't already be a feed for the specified currency.
    require(feedFor[_currency][_base] == AggregatorV3Interface(address(0)),
        "Prices::addFeedFor: ALREADY_EXISTS"
    );

    // Get a reference to the number of decimals the feed uses.
    uint256 _decimals = _feed.decimals();

    // Decimals should be less than or equal to the target number of decimals.
    require(
        _decimals <= targetDecimals,
        "Prices::addFeedFor: BAD_DECIMALS"
    );

    // Set the feed.
    feedFor[_currency][_base] = _feed;

    // Set the decimal adjuster for the currency.
    feedDecimalAdjusterFor[_currency][_base] = 10**(targetDecimals - _decimals);

    emit AddFeed(_currency, _base, _decimals, _feed);
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBPrices::addFeedFor: ALREADY_EXISTS`** | Thrown if the specified currency already has an associated price feed. |
| **`JBPrices::addFeedFor: BAD_DECIMALS`** | Thrown if the amount of decimals specified in the provided feed contract is greater than the [`targetDecimals`](../properties/targetdecimals.md). |
{% endtab %}

{% tab title="Events" %}
<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>AddFeed</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed currency</code>
          </li>
          <li><code>uint256 indexed base</code>
          </li>
          <li><code>uint256 decimals</code>
          </li>
          <li><code>AggregatorV3Interface feed</code>
          </li>
        </ul>
        <p><a href="../events/addfeed.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}



