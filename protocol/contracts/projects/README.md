---
description: 'Manages ownership over projects, which are represented as ERC-721 tokens.'
---

# Projects

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/Projects.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/Projects.sol)

### **Addresses**

Ethereum mainnet: [0x9b5a4053FfBB11cA9cd858AAEE43cc95ab435418](https://etherscan.io/address/0x9b5a4053FfBB11cA9cd858AAEE43cc95ab435418)  
Rinkeby testnet: [0x34A1d33e49907ADFE05fb4E21411a06a8c4300eE](https://rinkeby.etherscan.io/address/0x34A1d33e49907ADFE05fb4E21411a06a8c4300eE)

### **Interfaces**

| Name\` | Description |
| :--- | :--- |
| **`IProjects`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| **Contract** | Description |
| :--- | :--- |
| **`ERC721`** | A standard definition for non-fungible tokens \(NFTs\) |
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
          <li><code>ITerminal terminal</code> 
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
          <li><code>address indexed to</code> 
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
          <li><code>address indexed account</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
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
      <td style="text-align:left"><b><code>count</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 count</code>
          </li>
        </ul>
        <p><a href="read/count.md">more</a>
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
        <p><a href="read/uriof.md">more</a>
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
        <p><a href="read/handleof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>projectFor</code></b>
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
        <p><a href="read/projectfor.md">more</a>
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
        <p><a href="read/transferaddressfor.md">more</a>
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
        <p><a href="read/challengeexpiryof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>exists</code></b>
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
          <li><code>bool exists</code>
          </li>
        </ul>
        <p><a href="read/exists.md">more</a>
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
      <td style="text-align:left"><b><code>create</code></b>
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
          <li><code>ITerminal _terminal</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 projectId</code>
          </li>
        </ul>
        <p><a href="write/create.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setHandle</code></b>
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
        <p><a href="write/sethandle.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setUri</code></b>
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
        <p><a href="write/seturi.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>transferHandle</code></b>
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
          <li><code>address _to</code> 
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
        <p><a href="write/transferhandle.md">more</a>
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
      <td style="text-align:left"><b><code>renewHandle</code></b>
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
        <p><a href="write/renewhandle.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

