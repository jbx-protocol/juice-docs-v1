---
description: Manage and normalizes ETH price feeds.
---

# Prices

## Addresses

Ethereum mainnet: [0xa9537Cc42555564206D4E57c0eb6943d56E83A30](https://etherscan.io/address/0xa9537Cc42555564206D4E57c0eb6943d56E83A30)  
Rinkeby testnet: [0x22c2580c58D2F84165CD5af02729Af344cE7d637](https://rinkeby.etherscan.io/address/0x22c2580c58D2F84165CD5af02729Af344cE7d637)

## Overview

**Interfaces:**

| Name | Description |
| :--- | :--- |
| `IPrices` | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |

**Inheritance:**

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
      </td>
    </tr>
  </tbody>
</table>

