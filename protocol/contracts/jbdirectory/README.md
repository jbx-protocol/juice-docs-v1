---
description: >-
  Keeps a reference of which payment terminal contract each project is currently
  accepting funds through. When a project migrates from one terminal to another,
  the change is reflected in this contract.
---

# JBDirectory

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBDirectory.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/TerminalDirectory.sol)

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| **Name** | Description |
| :--- | :--- |
| **`IJBDirectory`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| **Name** | Description |
| :--- | :--- |
| **`JBOperatable`** | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.  |

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
      <td style="text-align:left"><b><code>SetTerminal</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>IJBTerminal indexed terminal</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/setterminal.md">more</a>
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
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>immutable</code>
          </li>
        </ul>
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
      <td style="text-align:left"><b><code>terminalOf</code></b>
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
          <li><code>IJBTerminal terminalOf</code>
          </li>
        </ul>
        <p><a href="read/terminalof.md">more</a>
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
      <td style="text-align:left"><b><code>setTerminal</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>IJBTerminal _terminal</code>
          </li>
        </ul>
        <p><a href="write/setterminal.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

