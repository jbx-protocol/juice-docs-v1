---
description: >-
  Manages all inflows and outflows of funds into the Juicebox ecosystem. This
  contract also stores all treasury funds for all projects.
---

# TerminalV2PaymentLayer

## Overview

**Interfaces:**

| Interface | Description |
| :--- | :--- |
| **`ITerminalV2PaymentLayer`** | General interface for the methods in this contract that send and receive funds according to the Juicebox protocol's rules. |

**Inheritance:**

| Contract | Description |
| :--- | :--- |
| **`Operatable`** | Includes convenience modifiers for checking a message sender's permissions before executing certain transactions.  |
| **`ReentrancyGuard`** | Includes convenience modifiers for preventing access to certain functions while certain other functions are being executed.  |

## Events

<table>
  <thead>
    <tr>
      <th style="text-align:left">Event</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>AddToBalance</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>uint256 value</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>Migrate</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>ITerminal indexed to</code>
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>DistributePayouts</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>UseAllowance</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleNumber</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>Pay</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleNumber</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>Redeem</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleNumber</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>DistributeToPayoutSplit</code>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleNumber</code>
          </li>
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
      </td>
    </tr>
  </tbody>
</table>

## Read

<table>
  <thead>
    <tr>
      <th style="text-align:left">Definition</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>projects</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>splitStore</code>
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
          <li><code>ISplitsStore splitsStore</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>terminalDirectory</code>
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
          <li><code>ITerminalDirectory terminalDirectory</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>dataLayer</code>
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
          <li><code>ITerminalV2DataLayer dataLayer</code>
          </li>
        </ul>
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
      <td style="text-align:left"><code>pay</code>
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
          <li><code>uint256 projectId</code>
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
          <li><code>uint256 fundingCycleNumber</code> 
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>distributePayouts</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>useAllowance</code>
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
          <li><code>uint256 fundingCycleConfiguration</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>redeemTokens</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>migrate</code>
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
          <li><code>ITerminalDataLayer _to</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>addToBalance</code>
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
        </ul>
      </td>
    </tr>
  </tbody>
</table>

