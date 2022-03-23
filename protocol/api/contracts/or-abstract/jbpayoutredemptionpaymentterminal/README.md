---
description: Manages all inflows and outflows of ETH into the Juicebox ecosystem.
---

# JBPayoutRedemptionPaymentTerminal

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBPayoutRedemptionPaymentTerminal.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\

### **Interfaces**

| Name                                             | Description                                                                                                                              |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBPayoutRedemptionPaymentTerminal`**](../../../interfaces/ijbpayo) | General interface for the methods in this contract that interact with the blockchain's state according to the protocol's rules. |

### **Inheritance**

| Contract                                                                  | Description                                                                                                                              |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`ReentrancyGuard`**](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard) | Contract module that helps prevent reentrant calls to a function. |


## Constructor

```solidity
constructor(
  address _token,
  uint256 _decimals,
  uint256 _currency,
  uint256 _baseWeightCurrency,
  uint256 _payoutSplitsGroup,
  IJBOperatorStore _operatorStore,
  IJBProjects _projects,
  IJBDirectory _directory,
  IJBSplitsStore _splitsStore,
  IJBPrices _prices,
  IJBPaymentTerminalStore _store,
  address _owner
) JBOperatable(_operatorStore) {
  token = _token;
  decimals = _decimals;
  baseWeightCurrency = _baseWeightCurrency;
  payoutSplitsGroup = _payoutSplitsGroup;
  currency = _currency;
  projects = _projects;
  directory = _directory;
  splitsStore = _splitsStore;
  prices = _prices;
  store = _store;

  transferOwnership(_owner);
}
```

* **Arguments:**
  * `_token` is the token that this terminal manages.
  * `_decimals` is the number of decimals the token fixed point amounts are expected to have.
  * `_currency` is the currency that this terminal's token adheres to for price feeds. From [`JBCurrencies`](../../../libraries/jbcurrencies.md).
  * `_baseWeightCurrency` is the currency to base token issuance on. From [`JBCurrencies`](../../../libraries/jbcurrencies.md).
  * `_payoutSplitsGroup` is the group that denotes payout splits from this terminal in the splits store. From [`JBSplitGroups`](../../../libraries/jbsplitsgroups.md).
  * `_operatorStore` is an [`IJBOperatorStore`](../../../interfaces/ijboperatorstore.md) contract storing operator assignments.
  * `_projects` is an [`IJBProjects`](../../../interfaces/ijbprojects.md) contract which mints ERC-721's that represent project ownership and transfers.
  * `_directory` is an [`IJBDirectory`](../../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.
  * `_splitsStore` is an [`IJBSplitsStore`](../../../interfaces/ijbsplitsstore/) contract that stores splits for each project.
  * `_prices` is an [`IJBPrices`](../../../interfaces/ijbprices.md) contract that exposes price feeds.
  * `_store` is a contract that stores the terminal's data.
  * `_owner` is the address that will own this contract.

## Events

| Name                                                               | Data                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`AddToBalance`**](events/addtobalance.md)                       | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                                                                                                                                                               |
| [**`Migrate`**](events/migrate.md)                                 | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)indexed to</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                                                                 |
| [**`DistributePayouts`**](events/distributepayouts.md)             | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 distributedAmount</code></li><li><code>uint256 feeAmount</code></li><li><code>uint256 beneficiaryDistributionAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                       |
| [**`UseAllowance`**](events/useallowance.md)                       | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 distributedAmount</code></li><li><code>uint256 feeAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                                                            |
| [**`ProcessFees`**](events/processfees.md)                         | <ul><li><a href="../../../data-structures/jbfee.md"><code>JBFee</code></a><code>[] fees</code></li><li><code>uint256 indexed projectId</code></li></ul>                                                                                                                                                                                                                                                                                                                         |
| [**`Pay`**](events/pay.md)                                         | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 beneficiaryTokenCount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>        |
| [**`DelegateDidPay`**](events/delegatedidpay.md)                                         | <ul><li><code>[`IJBPayDelegate`](../../../interfaces/ijbpaydelegate.md)indexed delegate</code></li><li><code>[`JBDidPayData`](../../../data-structures/jbdidpaydata.md)data</code></li><li><code>address caller</code></li></ul>        |
| [**`RedeemTokens`**](events/redeemtokens.md)                       | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>address holder</code></li><li><code>address beneficiary</code></li><li><code>uint256 tokenCount</code></li><li><code>uint256 claimedAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
| [**`DelegateDidRedeem`**](events/delegatedidredeem.md)                                         | <ul><li><code>[`IJBRedemptionDelegate`](../../../interfaces/ijbredemptiondelegate.md)indexed delegate</code></li><li><code>[`JBDidRedeemData`](../../../data-structures/jbdidredeemdata.md)data</code></li><li><code>address caller</code></li></ul>        |
| [**`DistributeToPayoutSplit`**](events/distributetopayoutsplit.md) | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><code>[`JBSplit`](../../../data-structures/jbsplit.md)split</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                           |
| [**`SetFee`**](events/setfee.md)                                                 | <ul><li><code>uint256 fee</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                                                                            |
| [**`SetFeeGauge`**](events/setfeegauge.md) | <ul><li><code>[`IJBFeeGauge`](../../../interfaces/ijbfeegauge.md)indexed feeGauge</code></li><li><code>address caller</code></li></ul> |

| [**`SetFeelessTerminal`**](events/setfeelessterminal.md) | <ul><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)indexed terminal</code></li><li><code>bool indexed flag</code></li><li><code>address caller</code></li></ul> |

## Properties

| Function                                                                  | Definition                                                                                                                                                                                                                                                               |
| ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`projects`**](properties/projects.md)                                  | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBProjects`](../../../interfaces/ijbprojects.md)projects</code></li></ul>                                     |
| [**`directory`**](properties/directory.md)                                | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBDirectory`](../../../interfaces/ijbdirectory.md)directory</code></li></ul>                                  |
| [**`splitStore`**](properties/splitstore.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBSplitsStore`](../../../interfaces/ijbsplitsstore.md)splitsStore</code></li></ul>                            |
| [**`prices`**](properties/prices.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBPrices`](../../../interfaces/ijbprices.md)prices</code></li></ul>                            |
| [**`store`**](properties/store.md)                                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)store</code></li></ul> |
| [**`token`**](properties/token.md)                                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>address token</code></li></ul> |
| [**`decimals`**](properties/decimals.md)                                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 decimals</code></li></ul> |
| [**`currency`**](properties/currency.md)                                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 currency</code></li></ul> |
| [**`baseWeightCurrency`**](properties/baseweightcurrency.md)                                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 baseWeightCurrency</code></li></ul> |
| [**`payoutSplitsGroup`**](properties/payoutsplitsgroup.md)                                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 payoutSplitsGroup</code></li></ul> |
| [**`fee`**](properties/fee.md)                                        | <p><strong>Returns</strong></p><ul><li><code>uint256 store</code></li></ul> |
| [**`feeGauge`**](properties/feegauge.md)                                        | <p><strong>Params</strong></p><ul><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)terminal</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBFeeGauge`](../../../interfaces/ijbfeegauge.md)feeGauge</code></li></ul> |
| [**`isFeelessTerminal`**](properties/isfeelessterminal.md)                                        | <p><strong>returns</strong></p><ul><li><code>bool</code></li></ul> |


## Read

| Function                                   | Definition                                                                                                                                                                                                                            |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`currentEthOverflowOf`**](read/currentethoverflowof) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 ethOverflow</code></li></ul>                                                                        |
| [**`heldFeesOf`**](read/heldfeesof.md)     | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFee`](../../../data-structures/jbfee.md)[] fees</code></li></ul> |

## Write

| Function                                                  | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`pay`**](write/pay.md)                                 | <p><strong>Traits</strong></p><ul><li><code>payable</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _amount</code></li><li><code>uint256 _projectId</code></li><li><code>address _beneficiary</code></li><li><code>uint256 _minReturnedTokens</code></li><li><code>bool _preferClaimedTokens</code></li><li><code>string _memo</code></li><li><code>bytes _metadata</code></li></ul>                                                                                                                                                          |
| [**`distributePayoutsOf`**](write/distributepayoutsof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _minReturnedTokens</code></li><li><code>string _memo</code></li></ul>                                                                                                                                                                                                                  |
| [**`useAllowanceOf`**](write/useallowanceof.md)           | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _minReturnedTokens</code></li><li><code>address payable _beneficiary</code></li><li><code>string _memo</code></li></ul>                                                                               |
| [**`redeemTokensOf`**](write/redeemtokensof.md)           | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li><li><code>uint256 _minReturnedTokens</code></li><li><code>address payable _beneficiary</code></li><li><code>string _memo</code></li><li><code>bytes _metadata</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 reclaimAmount</code></li></ul> |
| [**`migrate`**](write/migrate.md)                         | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)_to</code></li></ul>                                                                                                                                                                                                                             |
| [**`addToBalanceOf`**](write/addtobalanceof.md)           | <p><strong>Traits</strong></p><ul><li><code>payable</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>string _memo</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| [**`processFees`**](write/processfees.md)                 | <p><strong>Traits</strong></p><ul><li><code>[`requirePermissionAllowingOverride`](../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md)</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul>                                                                                                                                                                                                                                                                                                                                                |
| [**`setFee`**](write/setfee.md)                 | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _fee</code></li></ul>                                                                                                                                                                                                                                                                                                                                                |
| [**`setFeeGauge`**](write/setfeegauge.md)                 | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>[`IJBFeeGauge`](../../../interfaces/ijbfeegauge.md)_feeGauge</code></li></ul>                                                                                                                                                                                                                                                                                                                                                |
| [**`setFeelessTerminal`**](write/setfeelessterminal.md)                 | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>[`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)_terminal</code></li><li><code>bool _flag</code></li></ul>                                                                                                                                                                                                                                                                                                                                                |
