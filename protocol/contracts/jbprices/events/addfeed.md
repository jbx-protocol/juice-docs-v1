# AddFeed

Emitted from:

* [`addFeedFor`](../write/addfeed.md)

Definition:

```javascript
event AddFeed(
    uint256 indexed currency,
    uint256 indexed base,
    uint256 decimals,
    AggregatorV3Interface feed
);
```

* `currency` is the currency the feed was added for.
* `base` is the currency that the feed's price will be reported based on.
* `decimals` is the number of decimals that the feed reports.
* `feed` is the feed's contract.

