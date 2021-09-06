# Prices

### Read

```javascript
/** 
  @notice 
  Gets the current price of ETH for the provided currency.

  @param _currency The currency to get a price for.

  @return price The price of ETH with 18 decimals.
*/
function getETHPriceFor(uint256 _currency)
    external
    view
    override
    returns (uint256)
```

### Write

```javascript
/** 
  @notice 
  Add a price feed for the price of ETH.

  @dev
  Current feeds can't be modified.

  @param _feed The price feed being added.
  @param _currency The currency that the price feed is for.
*/
function addFeed(AggregatorV3Interface _feed, uint256 _currency)
    external
    override
    onlyOwner
```

