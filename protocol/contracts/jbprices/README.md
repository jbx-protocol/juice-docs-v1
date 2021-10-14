---
description: Manages and normalizes price feeds.
---

# JBPrices

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBPrices.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name                                             | Description                                                                                                                              |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBPrices`**](../../interfaces/ijbprices.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                                                  | Description                                                                                                                              |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Ownable`**](https://docs.openzeppelin.com/contracts/4.x/api/security) | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner. |

## Events

| Name          | Data                                                                                                                                                                                                                               |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`AddFeed`** | <ul><li><code>uint256 indexed currency</code></li><li><code>uint256 indexed base</code></li><li><code>uint256 decimals</code></li><li><code>AggregatorV3Interface feed</code></li></ul><p><a href="events/addfeed.md">more</a></p> |

## Properties

| Function                     | Definition                                                                                                                                                                                                                                               |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`targetDecimals`**         | <p><strong>Traits</strong></p><ul><li><code>constant</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 18</code></li></ul><p><a href="properties/targetdecimals.md">more</a></p>                                                      |
| **`feedDecimalAdjusterFor`** | <p><strong>Params</strong></p><ul><li><code>uint256 _currency</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 adjustmentAmount</code></li></ul><p><a href="properties/feeddecimaladjuster.md">more</a></p>                          |
| **`feedFor`**                | <p><strong>Params</strong></p><ul><li><code>uint256 _currency</code></li><li><code>uint256 _base</code></li></ul><p><strong>Returns</strong></p><ul><li><code>AggregatorV3Interface feed</code></li></ul><p><a href="properties/feedfor.md">more</a></p> |

## Read

| Function       | Definition                                                                                                                                                                                                                                    |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`priceFor`** | <p><strong>Params</strong></p><ul><li><code>uint256 _currency</code></li><li><code>uint256 _base</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 _price</code></li></ul><p><a href="read/getethpricefor.md">more</a></p> |

## Write

| Function         | Definition                                                                                                                                                                                                                                                                         |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`addFeedFor`** | <p><strong>Traits</strong></p><ul><li><code>onlyOwner</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _currency</code></li><li><code>uint256 _base</code></li><li><code>AggregatorV3Interface _feed</code></li></ul><p><a href="write/addfeed.md">more</a></p> |
