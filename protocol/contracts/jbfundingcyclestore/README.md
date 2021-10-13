---
description: 'Manage funding cycle configurations, ballots, accounting, and scheduling.'
---

# JBFundingCycleStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBFundingCycleStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name | Description |
| :--- | :--- |
| **`IJBFundingCycleStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract | Description |
| :--- | :--- |
| **`JBUtility`** | Includes convenience functionality for checking if the message sender is the current terminal of the project who data is being manipulated. |

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
          <li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>properties</code> 
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
          <li><code>uint256 indexed basedOn</code> 
          </li>
        </ul>
        <p><a href="events/init.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Properties

<table>
  <thead>
    <tr>
      <th style="text-align:left">Function</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
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
        <p><a href>more</a>
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
          <li><code>uint256 latestId</code>
          </li>
        </ul>
        <p><a href="properties/latestidof.md">more</a>
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
          <li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code>
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
          <li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code>
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
          <li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code>
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
          <li><a href="../../enums/jbballotstate.md"><code>JBBallotState</code></a><code>ballotState</code>
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
      <td style="text-align:left"><b><code>configureFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><a href="../jbutility/modifiers/onlycontroller.md"><code>onlyController</code></a>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>_properties</code> 
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
          <li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code>
          </li>
        </ul>
        <p><a href="write/configurefor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>tapFrom</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><a href="../jbutility/modifiers/onlycontroller.md"><code>onlyController</code></a>
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
          <li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code>
          </li>
        </ul>
        <p><a href="write/tapfrom.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

