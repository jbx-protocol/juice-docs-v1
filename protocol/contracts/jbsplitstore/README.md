---
description: Stores splits information for all groups of each project.
---

# JBSplitStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBSplitStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| **Name** | Description |
| :--- | :--- |
| **`IJBSplitStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>Name</b>
      </th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>JBOperatable</code></b>
      </td>
      <td style="text-align:left">
        <p>Includes convenience functionality for checking a message sender&apos;s
          permissions before executing certain transactions.</p>
        <p></p>
        <p><a href="../jboperatable/">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Constructor

```javascript
/** 
  @param _operatorStore A contract storing operator assignments.
  @param _jbDirectory The directory of terminals.
  @param _projects A Projects contract which mints ERC-721's that represent project ownership and transfers.
*/
constructor(
  IJBOperatorStore _operatorStore,
  IJBDirectory _directory,
  IJBProjects _projects
) JBOperatable(_operatorStore) {
  projects = _projects;
  directory = _directory;
}
```

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
          <li><code>uint256 indexed domain</code> 
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
      <td style="text-align:left"><b><code>projects</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>IJBProjects projects</code>
          </li>
        </ul>
        <p><a href="properties/projects.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>directory</code></b>
      </td>
      <td style="text-align:left">
        <p>Returns</p>
        <ul>
          <li><code>IJBTerminal terminal</code>
          </li>
        </ul>
        <p><a href="properties/directory.md">more</a>
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
      <td style="text-align:left"><b><code>splitsOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _domain</code> 
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
        <p><a href="read/splitsof.md">more</a>
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

