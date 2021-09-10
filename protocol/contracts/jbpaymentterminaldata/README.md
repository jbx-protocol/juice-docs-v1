---
description: >-
  Stitches together funding cycles and treasury tokens, making sure all activity
  is accounted for and correct.
---

# JBPaymentTerminalData

## Overview

### Code

[https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/JBPaymentTerminalData.sol](https://github.com/jbx-protocol/juice-juicehouse/blob/version/2/packages/hardhat/contracts/TerminalV2DataLayer.sol)

### **Addresses**

Ethereum mainnet: _Not yet deployed_  
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name | Description |
| :--- | :--- |
| **`IJBPaymentTerminalData`** | General interface for the methods in this contract that change the blockchain's state according to the Juicebox protocol's rules. |
| **`ITerminalDataLayer`** | Conforms to ITerminal, which allows projects to migrate to this contract from other ITerminals \(like TerminalV1\), and to facilitate a project's future migration decisions. |

### **Inheritance**

| **Contract** | Description |
| :--- | :--- |
| **`JBOperatable`** | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.  |
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
        <p><a href="events/setoverflowallowance.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DistributeReservedTokens</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed fundingCycleId</code> 
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
        <p><a href="events/distributereservedtokens.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DistributeToReservedTokenSplit</code></b>
      </td>
      <td style="text-align:left">
        <ul>
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
        <p><a href="events/distributetoreservedtokensplit.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>MintTokens</code></b>
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
        <p><a href="events/minttokens.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>BurnTokens</code></b>
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
        <p><a href="events/burntokens.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>SetPaymentLayerd</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>IJBTerminal terminal</code>
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="events/setpaymentlayer.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DelegateDidPay</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>IJBPayDelegate indexed delegate</code> 
          </li>
          <li><code>DidPayParam param</code>
          </li>
        </ul>
        <p><a href="events/delegatedidpay.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>DelegateDidRedeem</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>IJBRedemptionDelegate indexed delegate</code>
          </li>
          <li><code>DidRedeemParam param</code>
          </li>
        </ul>
        <p><a href="events/delegatedidredeem.md">more</a>
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
      <td style="text-align:left"><b><code>fundingCycleStore</code></b>
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
          <li><code>IJBFundingCycleStore fundingCycleStore</code>
          </li>
        </ul>
        <p><a href="read/fundingcycles.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>tokenStore</code></b>
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
          <li><code>IJBTokenStore tokenStore</code>
          </li>
        </ul>
        <p><a href="read/ticketbooth.md">more</a>
        </p>
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
          <li><code>IJBSplitStore splitStore</code>
          </li>
        </ul>
        <p><a href="read/splitsstore.md">more</a>
        </p>
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
          <li><code>IJBPrices prices</code>
          </li>
        </ul>
        <p><a href="read/prices.md">more</a>
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
          <li><code>IJBDirectory jbDirectory</code>
          </li>
        </ul>
        <p><a href="read/terminaldirectory.md">more</a>
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
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 balanceOf</code>
          </li>
        </ul>
        <p><a href="read/balanceof.md">more</a>
        </p>
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
        <p><a href="read/remainingoverflowallowanceof.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>paymentTerminal</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>IJBTerminal paymentTerminal</code>
          </li>
        </ul>
        <p><a href="read/paymentlayer.md">more</a>
        </p>
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
        <p><a href="read/fee.md">more</a>
        </p>
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
        <p><a href="read/currentoverflowof.md">more</a>
        </p>
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
        <p><a href="read/reservedtokenbalanceof.md">more</a>
        </p>
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
        <p><a href="read/claimableoverflowof.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Write

<table>
  <thead>
    <tr>
      <th style="text-align:left">Definition</th>
      <th style="text-align:left"></th>
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
        <p><a href="write/launchproject.md">more</a>
        </p>
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
          <li><code>FundingCycleMetadataV2 _metadata</code> 
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
        <p><a href="write/reconfigurefundingcycles.md">more</a>
        </p>
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
        <p><a href="write/minttokens.md">more</a>
        </p>
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
        <p><a href="write/burntokens.md">more</a>
        </p>
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
        <p><a href="write/distributereservedtokens.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordPayment</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
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
          <li><code>uint256 _minReturnedTokens</code> 
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
        <p><a href="write/recordpayment.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordWithdrawal</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
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
        <p><a href="write/recordwithdrawal.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordUsedAllowance</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
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
        <p><a href="write/recordusedallowance.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordRedemption</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
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
        <p><a href="write/recordredemption.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordPrepForMigration</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p>more</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordMigration</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
          </li>
        </ul>
        <p><b>Params</b>
        </p>
        <ul>
          <li><code>uint256 _projectId</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>uint256 balance</code>
          </li>
        </ul>
        <p><a href="write/recordmigration.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>recordAddedBalance</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>onlyPaymentTerminal</code>
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
        <p><a href="write/recordaddedbalance.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>setPaymentTerminal</code></b>
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
          <li><code>IJBTerminal _paymentTerminal</code>
          </li>
        </ul>
        <p><a href="write/setpaymentterminal.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

