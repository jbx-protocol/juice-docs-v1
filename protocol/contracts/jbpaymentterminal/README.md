---
description: >-
  Manages all inflows and outflows of funds into the Juicebox ecosystem. This
  contract also stores all treasury funds for all projects.
---

# JBPaymentTerminal

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBPaymentTerminal.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name | Description |
| :--- | :--- |
| **`IJBPaymentTerminal`** | General interface for the methods in this contract that send and receive funds according to the Juicebox protocol's rules. |
| **`IJBTerminal`** | Allows projects to migrate to this contract from other IJBTerminals \(like TerminalV1\), and to facilitate a project's future migration decisions. |

### **Inheritance**

<table>
  <thead>
    <tr>
      <th style="text-align:left">Contract</th>
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
    <tr>
      <td style="text-align:left"><b><code>ReentrancyGuard</code></b>
      </td>
      <td style="text-align:left">Includes convenience functionality for preventing access to certain functions
        while certain other functions are being executed.</td>
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
      <td style="text-align:left"><b><code>AddToBalance</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>uint256 value</code>
          </li>
          <li><code>string memo</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/addtobalance.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>TransferBalance</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>IJBTerminal indexed terminal</code>
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/transferbalance.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DistributePayouts</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code>
          </li>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>address projectOwner</code>
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>uint256 tappedAmount</code>
          </li>
          <li><code>uint256 feeAmount</code>
          </li>
          <li><code>uint256 projectOwnerTransferAmount</code>
          </li>
          <li><code>string memo</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/distributepayouts.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>UseAllowance</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code>
          </li>
          <li><code>uint256 indexed configuration</code>
          </li>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>address beneficiary</code>
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>uint256 feeAmount</code>
          </li>
          <li><code>uint256 transferAmount</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/useallowance.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Pay</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code>
          </li>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>address indexed beneficiary</code>
          </li>
          <li><code>FundingCycle fundingCycle</code>
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>uint256 weight</code>
          </li>
          <li><code>uint256 tokenCount</code>
          </li>
          <li><code>string memo</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/pay.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Redeem</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code>
          </li>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>address indexed holder</code>
          </li>
          <li><code>FundingCycle fundingCycle</code>
          </li>
          <li><code>address beneficiary</code>
          </li>
          <li><code>uint256 tokenCount</code>
          </li>
          <li><code>uint256 claimedAmount</code>
          </li>
          <li><code>string memo</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/redeem.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DistributeToPayoutSplit</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code>
          </li>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>Split split</code>
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/distributetopayoutsplit.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>AllowBalanceTransfer</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>IJBTerminal terminal</code>
          </li>
        </ul>
        <p><a href="events/allowmigration.md">more</a>
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
      <td style="text-align:left"><b><code>splitStore</code></b>
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
          <li><code>IJBSplitsStore splitsStore</code>
          </li>
        </ul>
        <p><a href="read/splitstore.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>directory</code></b>
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
          <li><code>IJBDirectory directory</code>
          </li>
        </ul>
        <p><a href="read/directory.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>data</code></b>
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
          <li><code>IJBPaymentTerminalData data</code>
          </li>
        </ul>
        <p><a href="read/data.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>balanceTransferIsAllowedTo</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>IJBTerminal _terminal</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>bool migrationIsAllowed</code>
          </li>
        </ul>
        <p><a href="read/balancetransferisallowedto.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>dataAuthority</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>address dataAuthority</code>
          </li>
        </ul>
        <p><a href="read/dataauthority.md">more</a>
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
      <td style="text-align:left"><b><code>pay</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>payable</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>address _beneficiary</code>
          </li>
          <li><code>uint256 _minReturnedTokens</code>
          </li>
          <li><code>bool _preferUnstakedTokens</code>
          </li>
          <li><code>string _memo</code>
          </li>
          <li><code>bytes _delegateMetadata</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 fundingCycleId</code>
          </li>
        </ul>
        <p><a href="write/pay-1.md">more</a> 
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>distributePayoutsOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>nonReentrant</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>uint256 _amount</code>
          </li>
          <li><code>uint256 _currency</code>
          </li>
          <li><code>uint256 _minReturnedWei</code>
          </li>
          <li><code>string _memo</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 fundingCycleId</code>
          </li>
        </ul>
        <p><a href="write/distributepayoutsof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>useAllowanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>nonReentrant</code>
          </li>
          <li><code>requirePermission</code>
          </li>
        </ul>
        <p>
          <br /><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>uint256 _amount</code>
          </li>
          <li><code>uint256 _currency</code>
          </li>
          <li><code>uint256 _minReturnedWei</code>
          </li>
          <li><code>address payable _beneficiary</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 fundingCycleId</code>
          </li>
        </ul>
        <p><a href="write/useallowanceof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>redeemTokensOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>nonReentrant</code>
          </li>
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
          <li><code>uint256 _tokenCount</code>
          </li>
          <li><code>uint256 _minReturnedWei</code>
          </li>
          <li><code>address payable _beneficiary</code>
          </li>
          <li><code>string _memo</code>
          </li>
          <li><code>bytes _delegateMetadata</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 claimAmount</code>
          </li>
        </ul>
        <p><a href="write/redeemtokensof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>transferBalanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>nonReentrant</code>
          </li>
          <li><code>requirePermission</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>IJBTerminal _terminal</code>
          </li>
        </ul>
        <p><a href="write/migrate.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>addToBalanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>payable</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>string _memo</code>
          </li>
        </ul>
        <p><a href="write/addtobalanceof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>allowBalanceTransferTo</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyOwner</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>IJBTerminal _terminal</code>
          </li>
        </ul>
        <p><a href="write/allowmigration.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>prepForBalanceTransferOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>nonReentrant</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><a href="write/prepformigrationof.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

