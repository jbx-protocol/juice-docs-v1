---
description: >-
  This contract manages all bookkeeping for inflows and outflows of funds for a
  terminal.
---

# JBETHPaymentTerminalStore

Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBETHPaymentTerminalStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\\

### **Inheritance**

## Constructor

```solidity
constructor(
  IJBPrices _prices,
  IJBProjects _projects,
  IJBDirectory _directory,
  IJBFundingCycleStore _fundingCycleStore,
  IJBTokenStore _tokenStore
) {
  prices = _prices;
  projects = _projects;
  directory = _directory;
  fundingCycleStore = _fundingCycleStore;
  tokenStore = _tokenStore;
}
```

* **Arguments:**
  * `_prices` is an [`IJBPrices`](../../../interfaces/ijbprices.md) contract that exposes price feeds.
  * `_projects` is an [`IJBProjects`](../../../interfaces/ijbprojects.md) contract which mints ERC-721's that represent project ownership and transfers.
  * `_directory` is an [`IJBDirectory`](../../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.
  * `_fundingCycleStore` is an [`IJBFundingCycleStore`](../../../interfaces/ijbfundingcyclestore.md) contract storing all funding cycle configurations.
  * `_tokenStore` is an [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) contract that manages token minting and burning.

## Events

| Name                                        | Data                                                                                                                                                                                                                                                |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DelegateDidPay`**](broken-reference)    | <ul><li><a href="../../../interfaces/ijbpaydelegate.md"><code>IJBPayDelegate</code></a><code>delegate</code></li><li><a href="../../../data-structures/jbdidpaydata.md"><code>JBDidPayData</code></a><code>data</code></li></ul>                    |
| [**`DelegateDidRedeem`**](broken-reference) | <ul><li><a href="../../../interfaces/ijbredemptiondelegate.md"><code>JBRedemptionDelegate</code></a><code>delegate</code></li><li><a href="../../../data-structures/jbdidredeemdata.md"><code>JBDidRedeemData</code></a><code>data</code></li></ul> |

## Properties

| Function                                                                 | Definition                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`projects`**](properties/projects.md)                                 | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbprojects.md"><code>IJBProjects</code></a><code>projects</code></li></ul>                            |
| [**`directory`**](properties/directory.md)                               | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbdirectory.md"><code>IJBDirectory</code></a><code>directory</code></li></ul>                         |
| [**`fundingCycleStore`**](properties/fundingcyclestore.md)               | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbfundingcyclestore.md"><code>IJBFundingCycleStore</code></a><code>fundingCycleStore</code></li></ul> |
| [**`tokenStore`**](properties/tokenstore.md)                             | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbtokenstore.md"><code>IJBTokenStore</code></a><code>tokenStore</code></li></ul>                      |
| [**`prices`**](properties/prices.md)                                     | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbprices.md"><code>IJBPrices</code></a><code>prices</code></li></ul>                                  |
| [**`balanceOf`**](properties/balanceof.md)                               | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 balance</code></li></ul>                                                                                     |
| [**`usedOverflowAllowanceOf`**](properties/usedoverflowallowanceof.md)   | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _configuration</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 usedOverflowAllowance</code></li></ul>                           |
| [**`usedDistributionLimitsOf`**](properties/useddistributionlimitsof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _fundingCycleId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 usedDistributionLimitsOf</code></li></ul>                       |
| [**`terminal`**](properties/terminal.md)                                 | <p><strong>Traits</strong></p><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>terminal</code></li></ul>                                                                    |

## Read

| Function                                                       | Definition                                                                                                                                                                                                      |
| -------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`currentOverflowOf`**](read/currentoverflowof.md)           | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 currentOverflow</code></li></ul>                                            |
| [**`currentTotalOverflowOf`**](read/currenttotaloverflowof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 currentTotalOverflow</code></li></ul>                                       |
| [**`reclaimableOverflowOf`**](read/reclaimableoverflowof.md)       | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 reclaimableOverflow</code></li></ul> |

## Write

| Function                                                      | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`recordPaymentFrom`**](write/recordpaymentfrom.md)         | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _payer</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _preferClaimedTokensAndBeneficiary</code></li><li><code>uint256 _minReturnedTokens</code></li><li><code>string _memo</code></li><li><code>bytes _delegateMetadata</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>uint256 weight</code></li><li><code>uint256 tokenCount</code></li><li><code>string memo</code></li></ul> |
| [**`recordDistributionFor`**](write/recorddistributionfor.md) | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _minReturnedWei</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>uint256 withdrawnAmount</code></li></ul>                                                                                                                                                                                                                |
| [**`recordUsedAllowanceOf`**](write/recordusedallowanceof.md) | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _minReturnedWei</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>uint256 withdrawnAmount</code></li></ul>                                                                                                                                                                                                                |
| [**`recordRedemptionFor`**](write/recordredemptionfor.md)     | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li><li><code>uint256 _minReturnedWei</code></li><li><code>address payable _beneficiary</code></li><li><code>string _memo</code></li><li><code>bytes _delegateMetadata</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>uint256 reclaimAmount</code></li><li><code>string memo</code></li></ul>                                                |
| [**`recordAddedBalanceFor`**](write/recordaddedbalancefor.md) | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul>                                                                                                                                                                                                                                                                                                                                                 |
| [**`recordMigration`**](write/recordmigration.md)             | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 balance</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| [**`claimfor`**](write/claimfor.md)                           | <p><strong>Params</strong></p><ul><li><a href="../../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>_terminal</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
