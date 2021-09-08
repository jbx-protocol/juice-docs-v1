---
description: >-
  Keeps a reference of which terminal contract each project is currently
  accepting funds through. When a project migrates from one terminal to another,
  the change is reflected in this contract.
---

# TerminalDirectory

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/TerminalDirectory.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/TerminalDirectory.sol)

### **Addresses**

Ethereum mainnet: [0x46C9999A2EDCD5aA177ed7E8af90c68b7d75Ba46](https://etherscan.io/address/0x46C9999A2EDCD5aA177ed7E8af90c68b7d75Ba46)  
Rinkeby testnet: [0x88d8c9E98E6EdE75252c2473abc9724965fe7474](https://rinkeby.etherscan.io/address/0x88d8c9E98E6EdE75252c2473abc9724965fe7474)

### **Interfaces**

| **Name** | Description |
| :--- | :--- |
| **`ITerminalDirectory`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| **Name** | Description |
| :--- | :--- |
| **`Operatable`** | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.  |

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
          <li><code>ITerminal indexed terminal</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>SetPayerPreferences</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed account</code> 
          </li>
          <li><code>address beneficiary</code> 
          </li>
          <li><code>bool preferUnstakedTickets</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DeployAddress</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>string memo</code> 
          </li>
          <li><code>address indexed caller</code>
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
          <li><code>IProjects projects</code>
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
          <li><code>ITerminal terminalOf</code>
          </li>
        </ul>
        <p><a href="read/terminalof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>beneficiaryOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _account</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>address beneficiaryOf</code>
          </li>
        </ul>
        <p><a href="read/beneficiaryof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>unstakedTicketsPreferenceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _account</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>bool unstakedTicketsPrefrenceOf</code>
          </li>
        </ul>
        <p><a href="read/unstakedticketspreferenceof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>addressesOf</code></b>
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
          <li><code>IDirectPaymentAddress[] addressesOf</code>
          </li>
        </ul>
        <p><a href="read/addressesof.md">more</a>
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
      <td style="text-align:left"><b><code>deployAddress</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>string _memo</code>
          </li>
        </ul>
        <p><a href="write/deployaddress.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setTerminal</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>ITerminal _terminal</code>
          </li>
        </ul>
        <p><a href="write/setterminal.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setPayerPreferences</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _beneficiary</code> 
          </li>
          <li><code>bool _preferUnstakedTickets</code>
          </li>
        </ul>
        <p><a href="write/setpayerpreferences.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

