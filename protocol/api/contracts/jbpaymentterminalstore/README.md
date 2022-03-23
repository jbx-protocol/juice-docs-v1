---
description: >-
  Manages all bookkeeping for inflows and outflows of funds from any IJBPaymentTerminal.
---

# JBPaymentTerminalStore

Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBPaymentTerminalStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\

### **Interfaces**

| Name                                             | Description                                                                                                                              |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBPaymentTerminalStore`**](../../interfaces/ijbpaymentterminalstore.md) | General interface for the methods in this contract that interact with the blockchain's state according to the protocol's rules. |

### **Inheritance**

| Contract                                                                  | Description                                                                                                                              |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`ReentrancyGuard`**](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard) | Contract module that helps prevent reentrant calls to a function. |

## Constructor

```solidity
constructor(
  IJBPrices _prices,
  IJBDirectory _directory,
  IJBFundingCycleStore _fundingCycleStore
) {
  prices = _prices;
  directory = _directory;
  fundingCycleStore = _fundingCycleStore;
}
```

* **Arguments:**
  * `_prices` is an [`IJBPrices`](../../interfaces/ijbprices.md) contract that exposes price feeds.
  * `_directory` is an [`IJBDirectory`](../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.
  * `_fundingCycleStore` is an [`IJBFundingCycleStore`](../../interfaces/ijbfundingcyclestore.md) contract storing all funding cycle configurations.

## Properties

| Function                                                                 | Definition                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`directory`**](properties/directory.md)                               | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBDirectory`](../../interfaces/ijbdirectory.md)directory</code></li></ul>                         |
| [**`fundingCycleStore`**](properties/fundingcyclestore.md)               | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBFundingCycleStore`](../../interfaces/ijbfundingcyclestore.md)fundingCycleStore</code></li></ul> |
| [**`prices`**](properties/prices.md)                                     | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBPrices`](../../interfaces/ijbprices.md)prices</code></li></ul>                                  |
| [**`balanceOf`**](properties/balanceof.md)                               | <p><strong>Params</strong></p><ul><li><code>[`IJBPaymentTerminal`](../../interfaces/ijbpaymentterminal.md)_terminal</code></li><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 balance</code></li></ul>                                                                                     |
| [**`usedOverflowAllowanceOf`**](properties/usedoverflowallowanceof.md)   | <p><strong>Params</strong></p><ul><li><code>[`IJBPaymentTerminal`](../../interfaces/ijbpaymentterminal.md)_terminal</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _configuration</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 usedOverflowAllowance</code></li></ul>                           |
| [**`usedDistributionLimitOf`**](properties/useddistributionlimitof.md) | <p><strong>Params</strong></p><ul><li><code>[`IJBPaymentTerminal`](../../interfaces/ijbpaymentterminal.md)_terminal</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _fundingCycleId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 usedDistributionLimitOf</code></li></ul>                       |

## Read

| Function                                                       | Definition                                                                                                                                                                                                      |
| -------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`currentOverflowOf`**](read/currentoverflowof.md)           | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 currentOverflow</code></li></ul>                                            |
| [**`currentTotalOverflowOf`**](read/currenttotaloverflowof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 currentTotalOverflow</code></li></ul>                                       |
| [**`currentReclaimableOverflowOf`**](read/currentreclaimableoverflowof.md)       | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 reclaimableOverflow</code></li></ul> |

## Write

| Function                                                      | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`recordPaymentFrom`**](write/recordpaymentfrom.md)         | <p><strong>Traits</strong></p><ul><li><code>[`nonReentrant`](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _payer</code></li><li><code>[`JBTokenAmount`](../../data-structures/jbtokenamount.md)_amount</code></li><li><code>uint256 _projectId</code></li><li><code>address _beneficiary</code></li><li><code>uint256 _baseWeightCurrency</code></li><li><code>string _memo</code></li><li><code>bytes _metadata</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li><li><code>uint256 tokenCount</code></li><li><code>[`IJBPayDelegate`](../../interfaces/ijbpaydelegate.md)delegate</code></li><li><code>string memo</code></li></ul> |
| [**`recordDistributionFor`**](write/recorddistributionfor.md) | <p><strong>Traits</strong></p><ul><li><code>[`nonReentrant`](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _balanceCurrency</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li><li><code>uint256 distributedAmount</code></li></ul>                                                                                                                                                                                                                |
| [**`recordUsedAllowanceOf`**](write/recordusedallowanceof.md) | <p><strong>Traits</strong></p><ul><li><code>[`nonReentrant`](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _balanceCurrency</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li><li><code>uint256 usedAmount</code></li></ul>                                                                                                                                                                                                                |
| [**`recordRedemptionFor`**](write/recordredemptionfor.md)     | <p><strong>Traits</strong></p><ul><li><code>[`nonReentrant`](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li><li><code>uint256 _balanceDecimals</code></li><li><code>uint256 _balanceCurrency</code></li><li><code>address payable _beneficiary</code></li><li><code>string _memo</code></li><li><code>bytes _metadata</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li><li><code>uint256 reclaimAmount</code></li><li><code>[`IJBRedemptionDelegate`](../../interfaces/ijbredemptiondelegate.md)delegate</code></li><li><code>string memo</code></li></ul>                                                |
| [**`recordAddedBalanceFor`**](write/recordaddedbalancefor.md) | <p><strong>Traits</strong></p><ul><li><code>[`nonReentrant`](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul>                                                                                                                                                                                                                                                                                                                                                 |
| [**`recordMigration`**](write/recordmigration.md)             | <p><strong>Traits</strong></p><ul><li><code>[`nonReentrant`](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard-nonReentrant--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 balance</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
