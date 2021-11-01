# AddFeed

Emitted from:

* [`addFeedFor`](../write/addfeed.md)

## Definition

```solidity
event AddFeed(
    uint256 indexed currency,
    uint256 indexed base,
    AggregatorV3Interface feed
);
```

* `currency` is the currency the feed was added for.
* `base` is the currency that the feed's price will be reported based on.
* `feed` is the feed's contract.
