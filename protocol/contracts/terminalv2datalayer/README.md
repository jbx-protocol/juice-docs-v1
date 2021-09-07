---
description: >-
  Stitches together funding cycles and treasury tokens. It makes sure all
  activity is accounted for and correct.
---

# TerminalV2DataLayer

## Overview

**Interfaces:**

| Name | Description |
| :--- | :--- |
| **`ITerminalV2DataLayer`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |
| **`ITerminalDataLayer`** | Conforms to ITerminal, which allows projects to migrate to this contract from other ITerminals \(like TerminalV1\), and to facilitate a project's future migration decisions. |

**Inheritance:**

| **Contract** | Description |
| :--- | :--- |
| **`Operatable`** | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.  |
| **`Ownable`** | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner. |
| **`ReentrancyGuard`** | Includes convenience functionality for preventing access to certain functions while certain other functions are being executed.   |

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
      <td style="text-align:left"><b><code>SetOverflowAllowance</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 indexed configuration</code> 
          </li>
          <li><code>uint256 amount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DistributeReservedTokens</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleNumber</code> 
          </li>
          <li><code>uint256 indexed projectId</code>
          </li>
          <li><code>address indexed beneficiary</code> 
          </li>
          <li><code>uint256 count</code>
          </li>
          <li><code>uint256 projectOwnerTokenCount</code> 
          </li>
          <li><code>string memo</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DistributeToReservedTokenSplit</code></b>
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
          <li><code>uint256 tokenCount</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>AllowMigration</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>ITerminal terminal</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Mint</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address indexed beneficiary</code> 
          </li>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>uint256 amount</code>
          </li>
          <li><code>uint256 currency</code>
          </li>
          <li><code>uint256 weight</code>
          </li>
          <li><code>uint256 count</code>
          </li>
          <li><code>string memo</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
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
          <li><code>uint256 count</code>
          </li>
          <li><code>string memo</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>SetPaymentLayer</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>ITerminalV2PaymentLayer paymentLayer</code>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>fundingCycles</code></b>
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
          <li><code>IFundingCycles fundingCycles</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>ticketBooth</code></b>
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
          <li><code>ITicketBooth ticketBooth</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>splitsStore</code></b>
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
          <li><code>ISplitStore splitStore</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>prices</code></b>
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
          <li><code>IPrices prices</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>terminalDirectory</code></b>
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
      <td style="text-align:left"><b><code>balanceOf</code></b>
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
          <li><code>uint256 balanceOf</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>migrationIsAllowed</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>ITerminal _terminal</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>bool migrationIsAllowed</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>remainingOverflowAllowanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>uint256 _configuration</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 remainingOverflowAllowanceOf</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>paymentLayer</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>ITerminalV2PaymentLayer paymentLayer</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>fee</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 fee</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>currentOverflowOf</code></b>
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
          <li><code>uint256 currentOverflowOf</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>reservedTokenBalanceOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>uint256 _reservedRate</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 reservedTokenBalanceOf</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>claimableOverflowOf</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
          <li><code>uint256 _tokenCount</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 claimableOverflowOf</code>
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
      <td style="text-align:left"><b><code>launchProject</code></b>
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
          <li><code>FundingCycleProperties _properties</code> 
          </li>
          <li><code>FundingCycleMetadataV2 _metadata</code> 
          </li>
          <li><code>uint256 _overflowAllowance</code> 
          </li>
          <li><code>Split[] _payoutSplits</code>
          </li>
          <li><code>Split[] _reservedTokenSplits</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>reconfigureFundingCycles</code></b>
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
          <li><code>FundingCycleProperties _properties</code>
          </li>
          <li> <code>FundingCycleMetadataV2 _metadata</code> 
          </li>
          <li><code>uint256 _overflowAllowance</code> 
          </li>
          <li><code>Split[] _payoutSplits</code> 
          </li>
          <li><code>Split[] _reservedTokenSplits</code>
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
      <td style="text-align:left"><b><code>mintTokens</code></b>
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
          <li><code>uint256 _amount</code>
          </li>
          <li><code>uint256 _currency</code> 
          </li>
          <li><code>uint256 _weight</code> 
          </li>
          <li><code>address _beneficiary</code> 
          </li>
          <li><code>string _memo</code>
          </li>
          <li><code>bool _preferUnstakedTokens</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 tokenCount</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>burnTokens</code></b>
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
          <li><code>string _memo</code> 
          </li>
          <li><code>bool _preferUnstakedTokens</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>distributeReservedTokens</code></b>
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
          <li><code>string _memo</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 tokenCount</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>prepForMigrationOf</code></b>
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
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordPayment</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentLayer</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>address _payer</code>
          </li>
          <li><code>uint256 _amount</code> 
          </li>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>uint256 _preferUnstakedTokensAndBeneficiary</code>
          </li>
          <li> <code>uint256 _minReturnedTokens</code> 
          </li>
          <li><code>string _memo</code> 
          </li>
          <li><code>bytes _delegateMetadata</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle fundingCycle</code> 
          </li>
          <li><code>uint256 weight</code> 
          </li>
          <li><code>uint256 tokenCount</code> 
          </li>
          <li><code>string memo</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordWithdrawal</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentLayer</code>
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
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle fundingCycle</code> 
          </li>
          <li><code>uint256 withdrawnAmount</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordUsedAllowance</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentLayer</code>
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
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle fundingCycle</code> 
          </li>
          <li><code>uint256 withdrawnAmount</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordRedemption</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentLayer</code>
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
          <li><code>address _beneficiary</code> 
          </li>
          <li><code>string _memo</code> 
          </li>
          <li><code>bytes _delegateMetadata</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>FundingCycle fundingCycle</code> 
          </li>
          <li><code>uint256 claimAmount</code> 
          </li>
          <li><code>string memo</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordMigration</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentLayer</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code> 
          </li>
          <li><code>ITerminal _to</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 balance</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordAddedBalance</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentLayer</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _amount</code> 
          </li>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>allowMigration</code></b>
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
          <li><code>ITerminal _terminal</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setPaymentLayer</code></b>
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
          <li><code>ITerminalV2PaymentLayer _paymentLayer</code>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>pay</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Note: This call forwards the payment and all params to <code>TerminalV2PaymentLayer.pay(...).</code></b>
        </p>
        <p>
          <br /><b>Traits</b>
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
          <li><code>string _memo</code> 
          </li>
          <li><code>bool _preferUnstakedTokens</code>
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
      <td style="text-align:left"><b><code>addToBalance</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Note: This call forwards the payment and all params to <code>TerminalV2PaymentLayer.addToBalance(...).</code></b>
          <br
          />
        </p>
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

