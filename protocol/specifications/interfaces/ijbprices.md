# IJBPrices

```solidity
interface IJBPrices {
  event AddFeed(uint256 indexed currency, uint256 indexed base, AggregatorV3Interface feed);

  function TARGET_DECIMALS() external returns (uint256);

  function feedFor(uint256 _currency, uint256 _base) external returns (AggregatorV3Interface);

  function priceFor(uint256 _currency, uint256 _base) external view returns (uint256);

  function addFeedFor(
    uint256 _currency,
    uint256 _base,
    AggregatorV3Interface _priceFeed
  ) external;
}
```
