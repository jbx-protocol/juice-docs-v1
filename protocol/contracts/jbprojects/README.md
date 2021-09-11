---
description: 'Manages ownership over projects, which are represented as ERC-721 tokens.'
---

# JBProjects

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBProjects.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name\` | Description |
| :--- | :--- |
| **`IJBProjects`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>Contract</b>
      </th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>ERC721</code></b>
      </td>
      <td style="text-align:left">A standard definition for non-fungible tokens (NFTs)</td>
    </tr>
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
      <td style="text-align:left"><b><code>Create</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>address indexed owner</code> 
          </li>
          <li><code>bytes32 indexed handle</code>
          </li>
          <li><code>string uri</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/create.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>SetHandle</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/sethandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>SetUri</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>string uri</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/seturi.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>TransferHandle</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>address indexed transferAddress</code> 
          </li>
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>bytes32 newHandle</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/transferhandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>ClaimHandle</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>address indexed transferAddress</code> 
          </li>
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/claimhandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>ChallengeHandle</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>uint256 challengeExpiry</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/challengehandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>RenewHandle</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/renewhandle.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Properties

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
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
        <p><a href="properties/count.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>uriOf</code></b>
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
          <li><code>string _uri</code>
          </li>
        </ul>
        <p><a href="properties/uriof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>handleOf</code></b>
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
          <li><code>bytes32 _handle</code>
          </li>
        </ul>
        <p><a href="properties/handleof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>idFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>bytes32 _handle</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><a href="properties/idfor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>transferAddressFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>bytes32 _handle</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>address _transferAddress</code>
          </li>
        </ul>
        <p><a href="properties/transferaddressfor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>challengeExpiryOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>bytes32 _handle</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 _challengeExpiryTimestamp</code>
          </li>
        </ul>
        <p><a href="properties/challengeexpiryof.md">more</a>
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
      <td style="text-align:left"><b><code>createFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _owner</code> 
          </li>
          <li><code>bytes32 _handle</code> 
          </li>
          <li><code>string _uri</code> 
          </li>
          <li><code>IJBTerminal _terminal</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 projectId</code>
          </li>
        </ul>
        <p><a href="write/createfor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setHandleOf</code></b>
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
          <li><code>bytes32 _handle</code>
          </li>
        </ul>
        <p><a href="write/sethandleof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setUriOf</code></b>
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
          <li><code>string _uri</code>
          </li>
        </ul>
        <p><a href="write/seturiof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>transferHandleOf</code></b>
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
          <li><code>address _transferAddress</code> 
          </li>
          <li><code>bytes32 _newHandle</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>bytes32 handle</code>
          </li>
        </ul>
        <p><a href="write/transferhandleof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>claimHandle</code></b>
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
          <li><code>bytes32 _handle</code> 
          </li>
          <li><code>address _for</code> 
          </li>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><a href="write/claimhandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>challengeHandle</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>bytes32 _handle</code>
          </li>
        </ul>
        <p><a href="write/challengehandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>renewHandleOf</code></b>
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
        </ul>
        <p><a href="write/renewhandleof.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

