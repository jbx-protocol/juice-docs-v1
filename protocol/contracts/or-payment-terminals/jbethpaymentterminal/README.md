---
description: Manages all inflows and outflows of ETH into the Juicebox ecosystem.
---

# JBETHPaymentTerminal

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBETHPaymentTerminal.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name                                                                        | Description                                                                                                                                      |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`IJBETHPaymentTerminal`**](../../../interfaces/ijbethpaymentterminal.md) | General interface for the methods in this contract that send and receive funds according to the Juicebox protocol's rules.                       |
| [**`IJBTerminal`**](../../../interfaces/ijbterminal.md)                     | Allows projects to migrate to this contract from other IJBTerminals (like TerminalV1), and to facilitate a project's future migration decisions. |

### **Inheritance**

| Contract                                                                          | Description                                                                                                                              |
| --------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBOperatable`**](../../or-abstract/jboperatable/)                             | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.                    |
| [**`Ownable`**](https://docs.openzeppelin.com/contracts/2.x/api/ownership)        | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner. |
| [**`ReentrancyGuard`**](https://docs.openzeppelin.com/contracts/4.x/api/security) | Includes convenience functionality for preventing access to certain functions while certain other functions are being executed.          |

## Constructor

```solidity
constructor(
  IJBOperatorStore _operatorStore,
  IJBProjects _projects,
  IJBDirectory _directory,
  IJBSplitsStore _splitsStore,
  IJBVault _vault,
  JBETHPaymentTerminalStore _store
) JBOperatable(_operatorStore) {
  projects = _projects;
  directory = _directory;
  splitsStore = _splitsStore;
  vault = _vault;
  store = _store;
}
```

* **Arguments:**
  * `_operatorStore` is an [`IJBOperatorStore`](../../../interfaces/ijboperatorstore.md) contract storing operator assignments.
  * `_projects` is an [`IJBProjects`](../../../interfaces/ijbprojects.md) contract which mints ERC-721's that represent project ownership and transfers.
  * `_directory` is an [`IJBDirectory`](../../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.
  * `_splitStore` is an [`IJBSplitStore`](../../../interfaces/ijbsplitstore/) contract that stores splits for each project.
  * `_vault` is an [`IJBVault`](../../../interfaces/ijbvault/) contract to store funds in.
  * `_store` is a contract that stores the terminal's data.

## Events

| Name                                                               | Data                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`AddToBalance`**](events/addtobalance.md)                       | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 value</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                                                                                                                                                |
| [**`Migrate`**](events/migrate.md)                                 | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                                                                 |
| [**`DistributePayouts`**](events/distributepayouts.md)             | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address projectOwner</code></li><li><code>uint256 amount</code></li><li><code>uint256 tappedAmount</code></li><li><code>uint256 feeAmount</code></li><li><code>uint256 projectOwnerTransferAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                        |
| [**`UseAllowance`**](events/useallowance.md)                       | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed configuration</code></li><li><code>uint256 indexed projectId</code></li><li><code>address beneficiary</code></li><li><code>uint256 amount</code></li><li><code>uint256 feeAmount</code></li><li><code>uint256 transferAmount</code></li><li><code>address caller</code></li></ul>                                                                                                             |
| [**`ProcessFees`**](events/processfees.md)                         | <ul><li><a href="../../../data-structures/jbfee.md"><code>JBFee</code></a><code>[] fees</code></li><li><code>uint256 indexed projectId</code></li></ul>                                                                                                                                                                                                                                                                                                                         |
| [**`Pay`**](events/pay.md)                                         | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed beneficiary</code></li><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>uint256 amount</code></li><li><code>uint256 weight</code></li><li><code>uint256 tokenCount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>        |
| [**`Redeem`**](events/redeem.md)                                   | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed holder</code></li><li><a href="../../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li><li><code>address beneficiary</code></li><li><code>uint256 tokenCount</code></li><li><code>uint256 claimedAmount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
| [**`DistributeToPayoutSplit`**](events/distributetopayoutsplit.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><a href="../../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>split</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul>                                                                                                                                                                                           |

## Properties

| Function                                   | Definition                                                                                                                                                                                                                     |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`projects`**](properties/projects.md)   | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbprojects.md"><code>IJBProjects</code></a><code>projects</code></li></ul>          |
| [**`directory`**](properties/directory.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbdirectory.md"><code>IJBDirectory</code></a><code>directory</code></li></ul>       |
| [**`splitStore`**](properties/splitstore.md)      | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbsplitsstore.md"><code>IJBSplitsStore</code></a><code>splitsStore</code></li></ul> |
| [**`vault`**](properties/vault.md)         | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../interfaces/ijbvault.md"><code>IJBVault</code></a><code>vault</code></li></ul>                   |
| [**`store`**](properties/store.md)     | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../jbethpaymentterminalstore/"><code>JBETHPaymentTerminalStore</code></a><code>store</code></li></ul>    |

## Read

| Function                                      | Definition                                                                                                                                                                                                             |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`heldFeesOf`**](read/heldfeesof.md)        | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../../data-structures/jbfee.md"><code>JBFee</code></a><code>[] fees</code></li></ul> |
| [**`delegate`**](read/delegate.md) | <p><strong>Returns</strong></p><ul><li><code>address claimableOverflow</code></li></ul>                                                                                                                                |

## Write

| Function                                                  | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`pay`**](write/pay.md)                               | <p><strong>Traits</strong></p><ul><li><code>payable</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>address _beneficiary</code></li><li><code>uint256 _minReturnedTokens</code></li><li><code>bool _preferClaimedTokens</code></li><li><code>string _memo</code></li><li><code>bytes _delegateMetadata</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 fundingCycleId</code></li></ul>                                                                                                                                                          |
| [**`distributePayoutsOf`**](write/distributepayoutsof.md) | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _minReturnedWei</code></li><li><code>string _memo</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 fundingCycleId</code></li></ul>                                                                                                                                                                                                                  |
| [**`useAllowanceOf`**](write/useallowanceof.md)           | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li><li><a href="../../or-abstract/jboperatable/modifiers/requirepermission.md"><code>requirePermission</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>uint256 _currency</code></li><li><code>uint256 _minReturnedWei</code></li><li><code>address payable _beneficiary</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 fundingCycleId</code></li></ul>                                                                               |
| [**`redeemTokensOf`**](write/redeemtokensof.md)           | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li><li><a href="../../or-abstract/jboperatable/modifiers/requirepermission.md"><code>requirePermission</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li><li><code>uint256 _minReturnedWei</code></li><li><code>address payable _beneficiary</code></li><li><code>string _memo</code></li><li><code>bytes _delegateMetadata</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 claimAmount</code></li></ul> |
| [**`migrate`**](write/migrate.md)                         | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li><li><a href="../../or-abstract/jboperatable/modifiers/requirepermission.md"><code>requirePermission</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>_to</code></li></ul>                                                                                                                                                                                                                                            |
| [**`addToBalanceOf`**](write/addtobalanceof.md)           | <p><strong>Traits</strong></p><ul><li><code>payable</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>string _memo</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| [**`processFees`**](write/processfees.md)                 | <p><strong>Traits</strong></p><ul><li><a href="../../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md"><code>requirePermissionAllowingOverride</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul>                                                                                                                                                                                                                                                                                                                                                |
