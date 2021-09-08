---
description: 'Manage funding cycle configurations, ballots, accounting, and scheduling.'
---

# FundingCycles

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/FundingCycles.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/FundingCycles.sol)

### **Addresses**

Ethereum mainnet: [0xf507B2A1dD7439201eb07F11E1d62AfB29216e2E](https://etherscan.io/address/0xf507B2A1dD7439201eb07F11E1d62AfB29216e2E)  
Rinkeby testnet: [0x234985c467E90483bA42fB2C7C2f71A1874D805D](https://rinkeby.etherscan.io/address/0x234985c467E90483bA42fB2C7C2f71A1874D805D)

### **Interfaces**

| Name | Description |
| :--- | :--- |
| **`IFundingCycles`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract | Description |
| :--- | :--- |
| **`TerminalUtility`** | Includes convenience functionality for checking if the message sender is the current terminal of the project who data is being manipulated. |

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
      <td style="text-align:left"><b><code>Configure</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 reconfigured</code> 
          </li>
          <li><code>FundingCycleProperties _properties</code> 
          </li>
          <li><code>uint256 metadata</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Tap</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>uint256 newTappedAmount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Init</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 number</code> 
          </li>
          <li><code>uint256 previous</code> 
          </li>
          <li><code>uint256 weight</code> 
          </li>
          <li><code>uint256 start</code>
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
      <td style="text-align:left"><b><code>BASE_WEIGHT</code></b>
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
          <li><code>uint256 1E24</code>
          </li>
        </ul>
        <p><a href="read/base_weight.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>MAX_CYCLE_LIMIT</code></b>
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
          <li><code>uint256 32</code>
          </li>
        </ul>
        <p><a href="read/max_cycle_limit.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>latestIdOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 _latestIdOf</code>
          </li>
        </ul>
        <p><a href="read/latestidof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>count</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 count</code>
          </li>
        </ul>
        <p><a href="../projects/read/count.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>get</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _fundingCycleId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle _fundingCycle</code>
          </li>
        </ul>
        <p><a href="read/get.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>queuedOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle _fundingCycle</code>
          </li>
        </ul>
        <p><a href="read/queuedof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>currentOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle _fundingCycle</code>
          </li>
        </ul>
        <p><a href="read/currentof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>currentBallotStateOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>BallotState _ballotState</code>
          </li>
        </ul>
        <p><a href="read/currentballotstateof.md">more</a>
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
      <td style="text-align:left"><b><code>configure</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyTerminal</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>FundingCycleProperties _properties</code> 
          </li>
          <li><code>uint256 _metadata</code> 
          </li>
          <li><code>uint256 _fee</code> 
          </li>
          <li><code>bool _configureActiveFundingCycle</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle _fundingCycle</code>
          </li>
        </ul>
        <p><a href="write/configure.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>tap</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyTerminal</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle fundingCycle</code>
          </li>
        </ul>
        <p><a href="write/tap.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

