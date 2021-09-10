---
description: >-
  Manages token minting and burning for all projects, while keeping track of
  staked/unstaked and locked/unlock token balances for all accounts.
---

# JBTokenStore

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBTokenstore.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/TicketBooth.sol)

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| **Name** | Description |
| :--- | :--- |
| **`IJBTokenStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| **Name** | Description |
| :--- | :--- |
| **`JBOperatable`** | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.  |
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
      <td style="text-align:left"><b><code>Issue</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>IJBToken indexed token</code> 
          </li>
          <li><code>string name</code> 
          </li>
          <li><code>string symbol</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/issue.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Mint</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>bool shouldUnstakeTokens</code> 
          </li>
          <li><code>bool preferUnstakedTokens</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/print.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Burn</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code>
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>uint256 inlockedStakedBalance</code> 
          </li>
          <li><code>bool preferUnstakedTokens</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="../jbpaymentterminal/events/redeem.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Stake</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/stake.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Unstake</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/unstake.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Lock</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/lock.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Unlock</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/unlock.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Transfer</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed holder</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>address indexed recipient</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/transfer.md">more</a>
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
      <td style="text-align:left"><b><code>tokenOf</code></b>
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
          <li><code>IJBToken token</code>
          </li>
        </ul>
        <p><a href="read/ticketsof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>stakedBalanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _holder</code>
          </li>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 stakedBalance</code>
          </li>
        </ul>
        <p><a href="read/stakedbalanceof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>stakedTotalSupplyOf</code></b>
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
          <li><code>uint256 stakedTotalSupply</code>
          </li>
        </ul>
        <p><a href="read/stakedtotalsupplyof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>lockedBalanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _holder</code>
          </li>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 lockedBalance</code>
          </li>
        </ul>
        <p><a href="read/lockedbalanceof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>lockedBalanceBy</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _locker</code>
          </li>
          <li><code>address _holder</code>
          </li>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 lockedBalance</code>
          </li>
        </ul>
        <p><a href="read/lockedbalanceby.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>totalSupplyOf</code></b>
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
          <li><code>uint256 totalSupply</code>
          </li>
        </ul>
        <p><a href="read/totalsupplyof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>balanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _holder</code>
          </li>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 balance</code>
          </li>
        </ul>
        <p><a href="../jbpaymentterminaldata/read/balanceof.md">more</a>
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
      <td style="text-align:left"><b><code>issueFor</code></b>
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
          <li><code>string _name</code> 
          </li>
          <li><code>string _symbol</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>IJBToken token</code>
          </li>
        </ul>
        <p><a href="write/issuefor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>mintFor</code></b>
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
          <li><code>address _holder</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code> 
          </li>
          <li><code>bool _preferUnstakedTokens</code>
          </li>
        </ul>
        <p><a href="write/mint.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>burnFrom</code></b>
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
          <li><code>address _holder</code>
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code> 
          </li>
          <li><code>bool _preferUnstakedTokens</code>
          </li>
        </ul>
        <p><a href="write/burn.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>stakeFor</code></b>
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
          <li><code>address _holder</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code>
          </li>
        </ul>
        <p><a href="write/stakefor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>unstakeFor</code></b>
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
          <li><code>address _holder</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code>
          </li>
        </ul>
        <p><a href="write/unstakefor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>lockFor</code></b>
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
          <li><code>address _holder</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code>
          </li>
        </ul>
        <p><a href="write/lockfor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>unlockFor</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _holder</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code>
          </li>
        </ul>
        <p><a href="write/unlockfor.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>transferTo</code></b>
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
          <li><code>address _recipient</code>
          </li>
          <li><code>address _holder</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _amount</code> 
          </li>
        </ul>
        <p><a href="write/transferto.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

