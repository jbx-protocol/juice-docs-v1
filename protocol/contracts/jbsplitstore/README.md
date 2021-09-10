---
description: Stores splits information for all groups of each project.
---

# JBSplitStore

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBSplitStore.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/SplitStore.sol)

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| **Name** | Description |
| :--- | :--- |
| **`IJBSplitStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| **Name** | Description |
| :--- | :--- |
| **`JBOperatable`** | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.  |
| **`JBTerminalUtility`** | Includes convenience functionality for checking if the message sender is the current terminal of the project who data is being manipulated. |

## Events

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>Name</b>
      </th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>SetSplit</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 indexed configuration</code> 
          </li>
          <li><code>uint256 indexed group</code> 
          </li>
          <li><code>Split split</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/setsplit.md">more</a>
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
      <td style="text-align:left"><b><code>projects</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>IJBProjects projects</code>
          </li>
        </ul>
        <p><a href="read/projects.md">more</a>
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
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _configuration</code> 
          </li>
          <li><code>uint256 _group</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>Split[] splits</code>
          </li>
        </ul>
        <p><a href="read/get.md">more</a>
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
      <td style="text-align:left"><b><code>set</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>requirePermission</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _configuration</code> 
          </li>
          <li><code>uint256 _group</code> 
          </li>
          <li><code>Split[] _splits</code>
          </li>
        </ul>
        <p><a href="write/set.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

