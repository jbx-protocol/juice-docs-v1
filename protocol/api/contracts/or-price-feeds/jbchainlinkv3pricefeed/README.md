---
description: Manages and normalizes price feeds.
---

# JBChainlinkV3PriceFeed

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBChainlinkV3PriceFeed.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_

### **Interfaces**

| Name                                             | Description                                                                                                                              |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBPriceFeed`**](../../../interfaces/ijbpricefeed.md) | General interface for the methods in this contract that interact with the blockchain's state according to the protocol's rules. |

## Constructor

```solidity
constructor(AggregatorV3Interface _feed) {
  feed = _feed;
}
```

* **Arguments:**
  * `_feed` is the feed to report prices from.

## Properties

| Function                                                          | Definition                                                                                                                                                                                                |
| ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`feed`**](properties/feed.md)                            | <p><strong>Returns</strong></p><ul><li><code>[`AggregatorV3Interface`](https://docs.chain.link/docs/price-feeds-api-reference/)feed</code></li></ul> |

## Read

| Function                                 | Definition                                                                                                                                                                                   |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`currentPrice`**](read/currentprice.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _decimals</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 price</code></li></ul> |
