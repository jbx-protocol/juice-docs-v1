---
description: >-
  Stores operator permissions for all addresses. Addresses can give permissions
  to any other address to take specific actions throughout the Juicebox
  ecosystem on their behalf.
---

# OperatorStore

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/OperatorStore.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/OperatorStore.sol)

### **Addresses**

Ethereum mainnet: [0xab47304D987390E27Ce3BC0fA4Fe31E3A98B0db2](https://etherscan.io/address/0xab47304D987390E27Ce3BC0fA4Fe31E3A98B0db2)  
Rinkeby testnet: [0x5dA2E4Ba83569fb7A22a4aDE14bBEf1236168406](https://rinkeby.etherscan.io/address/0x5dA2E4Ba83569fb7A22a4aDE14bBEf1236168406)

### **Interfaces**

| **Name** | Description |
| :--- | :--- |
| **`IOperatorStore`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |

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
      <td style="text-align:left"><b><code>SetOperator</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed operator</code> 
          </li>
          <li><code>address indexed account</code> 
          </li>
          <li><code>uint256 indexed domain</code> 
          </li>
          <li><code>uint256[] permissionIndexes</code> 
          </li>
          <li><code>uint256 packed</code>
          </li>
        </ul>
        <p><a href="events/setoperator.md">more</a>
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
      <td style="text-align:left"><b><code>permissionsOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _operator</code>
          </li>
          <li><code>address _account</code>
          </li>
          <li><code>uint256 _domain</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 permissions</code>
          </li>
        </ul>
        <p><a href="read/permissionsof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>hasPermission</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _operator</code> 
          </li>
          <li><code>address _account</code> 
          </li>
          <li><code>uint256 _domain</code> 
          </li>
          <li><code>uint256 _permissionIndex</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>bool hasPermission</code>
          </li>
        </ul>
        <p><a href="read/haspermission.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>hasPermissions</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _operator</code> 
          </li>
          <li><code>address _account</code> 
          </li>
          <li><code>uint256 _domain</code> 
          </li>
          <li><code>uint256[] _permissionIndexes</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>bool hasPermissions</code>
          </li>
        </ul>
        <p><a href="read/haspermissions.md">more</a>
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
      <td style="text-align:left"><b><code>setOperator</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _operator</code> 
          </li>
          <li><code>uint256 _domain</code> 
          </li>
          <li><code>uint256[] _permissionIndexes</code>
          </li>
        </ul>
        <p><a href="write/setoperator.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setOperators</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address[] _operators</code> 
          </li>
          <li><code>uint256[] _domains</code> 
          </li>
          <li><code>uint256[][] _permissionIndexes</code>
          </li>
        </ul>
        <p><a href="write/setoperators.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

