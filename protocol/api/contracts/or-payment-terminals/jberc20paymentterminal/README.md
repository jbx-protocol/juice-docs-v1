---
description: Manages all inflows and outflows of an ERC20 into the protocol ecosystem.
---

# JBETHPaymentTerminal

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBERC20PaymentTerminal.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_

### **Inheritance**

| Contract                                             | Description                                                                                                                              |
| ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBPayoutRedemptionPaymentTerminal`**](../../../interfaces/ijbpayoutredemptionpaymentterminal.md) | Generic terminal managing all inflows and outflows of funds into the protocol ecosystem. |

## Constructor

```solidity
  constructor(
    IERC20Metadata _token,
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
  )
    JBPayoutRedemptionPaymentTerminal(
      address(_token),
      _token.decimals(),
      _currency,
      _baseWeightCurrency,
      _payoutSplitsGroup,
      _operatorStore,
      _projects,
      _directory,
      _splitsStore,
      _prices,
      _store,
      _owner
    )
  {}
```

* **Arguments:**
  * `_token` is the ERC20 token that this terminal manages.
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
