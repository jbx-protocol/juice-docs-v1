---
description: 'Manage funding cycle configurations, ballots, accounting, and scheduling.'
---

# JBFundingCycleStore

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBFundingCycleStore.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/FundingCycles.sol)

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name | Description |
| :--- | :--- |
| **`IFundingCycleStore`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |

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
        <p><a href="events/configure.md">more</a>
        </p>
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
        <p><a href="events/tap.md">more</a>
        </p>
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
          <li><code>uint256 indexed number</code> 
          </li>
          <li><code>uint256 basedOn</code> 
          </li>
          <li><code>uint256 weight</code> 
          </li>
          <li><code>uint256 start</code>
          </li>
        </ul>
        <p><a href="events/init.md">more</a>
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

