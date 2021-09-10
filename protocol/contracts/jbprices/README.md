---
description: Manage and normalizes ETH price feeds.
---

# JBPrices

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBPrices.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/Prices.sol)

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name | Description |
| :--- | :--- |
| **`IJBPrices`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| **Contract** | Description |
| :--- | :--- |
| **`Ownable`** | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner. |

## Events

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
          <li><code>AggregatorV3Interface indexed feed</code>
          </li>
        </ul>
        <p><a href="events/addfeed.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Read

<table>
  <thead>
    <tr>
      <th style="text-align:left">Function</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>targetDecimals</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>constant</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 18</code>
          </li>
        </ul>
        <p><a href="read/targetdecimals.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>feedDecimalAdjuster</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _currency</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 adjustmentAmount</code>
          </li>
        </ul>
        <p><a href="read/feeddecimaladjuster.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>feedFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _currency</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>AggregatorV3Interface feed</code>
          </li>
        </ul>
        <p><a href="read/feedfor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>getETHPriceFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _currency</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 _price</code>
          </li>
        </ul>
        <p><a href="read/getethpricefor.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Write

<table>
  <thead>
    <tr>
      <th style="text-align:left">Function</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>addFeed</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyOwner</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>AggregatorV3Interface _feed</code> 
          </li>
          <li><code>uint256 _currency</code>
          </li>
        </ul>
        <p><a href="write/addfeed.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

