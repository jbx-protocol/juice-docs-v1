---
description: >-
  Stitches together funding cycles and treasury tokens, making sure all activity
  is accounted for and correct.
---

# JBController

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBPaymentTerminalData.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name                                                     | Description                                                                                                                                                     |
| -------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBController`**](../../interfaces/ijbcontroller.md) | General interface for the generic controller methods in this contract that interacts with funding cycles and tokens according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                                                          | Description                                                                                                                               |
| --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBTerminalUtility`**](../jbterminalutility/)                                  | Includes convenience functionality for checking if the message sender is a current terminal of the project who data is being manipulated. |
| [**`JBOperatable`**](../jboperatable/)                                            | Includes convenience functionality for checking a message sender's permissions before executing certain transactions.                     |
| [**`Ownable`**](https://docs.openzeppelin.com/contracts/2.x/api/ownership)        | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner.  |
| [**`ReentrancyGuard`**](https://docs.openzeppelin.com/contracts/4.x/api/security) | Includes convenience functionality for preventing access to certain functions while certain other functions are being executed.           |



## Constructor

```solidity
constructor(
  IJBOperatorStore _operatorStore,
  IJBProjects _projects,
  IJBDirectory _directory,
  IJBFundingCycleStore _fundingCycleStore,
  IJBTokenStore _tokenStore,
  IJBSplitsStore _splitsStore
) JBTerminalUtility(_directory) JBOperatable(_operatorStore) {
  projects = _projects;
  fundingCycleStore = _fundingCycleStore;
  tokenStore = _tokenStore;
  splitsStore = _splitsStore;
}
```

* Arguments:
  * `_operatorStore` A [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.
  * `_operatorStore` is an [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.
  * `_projects` is an [`IJBProjects`](../../interfaces/ijbprojects.md) contract which mints ERC-721's that represent project ownership and transfers.
  * `_directory` is an [`IJBDirectory`](../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.
  * `_fundingCycleStore` is an [`IJBFundingCycleStore`](../../interfaces/ijbfundingcyclestore.md) contract storing all funding cycle configurations.
  * `_tokenStore` is an [`IJBTokenStore`](../../interfaces/ijbtokenstore.md) contract that manages token minting and burning.
  * `_splitStore` is an [`IJBSplitStore`](../jbsplitstore/) contract that stores splits for each project.

## Events

| Name                                                                             | Data                                                                                                                                                                                                                                                                                                                      |
| -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetOverflowAllowance`**](events/setoverflowallowance.md)                     | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed configuration</code></li><li><a href="../../data-structures/jboverflowallowance.md"><code>JBOverflowAllowance</code></a><code>allowance</code></li><li><code>address caller</code></li></ul>                                                 |
| [**`DistributeReservedTokens`**](events/distributereservedtokens.md)             | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>address indexed beneficiary</code></li><li><code>uint256 count</code></li><li><code>uint256 projectOwnerTokenCount</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul> |
| [**`DistributeToReservedTokenSplit`**](events/distributetoreservedtokensplit.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>split</code></li><li><code>uint256 tokenCount</code></li><li><code>address caller</code></li></ul>                                    |
| [**`MintTokens`**](events/minttokens.md)                                         | <ul><li><code>address indexed beneficiary</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 count</code></li><li><code>string memo</code></li><li><code>bool shouldReserveTokens</code></li><li><code>address caller</code></li></ul>                                                           |
| [**`BurnTokens`**](events/burntokens.md)                                         | <ul><li><code>address indexed holder</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 count</code></li><li><code>string memo</code></li><li><code>address caller</code></li></ul>                                                                                                              |
| [**`Migrate`**](events/migrate.md)                                               | <ul><li><code>uint256 projectId</code></li><li><a href="../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>to</code></li><li><code>address caller</code></li></ul>                                                                                                                                    |

## Properties



| Function                                                        | Definition                                                                                                                                                                                                                                                      |
| --------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`projects`**](properties/projects.md)                        | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbprojects.md"><code>IJBProjects</code></a><code>projects</code></li></ul>                                              |
| [**`fundingCycleStore`**](properties/fundingcyclestore.md)      | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbfundingcyclestore.md"><code>IJBFundingCycleStore</code></a><code>fundingCycleStore</code></li></ul>                   |
| [**`tokenStore`**](properties/tokenstore.md)                    | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbtokenstore.md"><code>IJBTokenStore</code></a><code>tokenStore</code></li></ul>                                        |
| [**`splitsStore`**](../jbethpaymentterminal/read/splitstore.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbsplitsstore.md"><code>IJBSplitStore</code></a><code>splitStore</code></li></ul>                                       |
| [**`overflowAllowanceOf`**](properties/overflowallowanceof.md)  | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _configuration</code></li><li><code>IJBTerminal _terminal</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 overflowAllowanceOf</code></li></ul> |
| [**`fee`**](properties/fee.md)                                  | <p><strong>Returns</strong></p><ul><li><code>uint256 fee</code></li></ul>                                                                                                                                                                                       |

## Read

| Function                                                       | Definition                                                                                                                                                                                                                                                                     |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`reservedTokenBalanceOf`**](read/reservedtokenbalanceof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _reservedRate</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 reservedTokenBalanceOf</code></li></ul><p><a href="read/reservedtokenbalanceof.md">more</a></p> |

## Write

| Definition                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`launchProjectFor`**](write/launchprojectfor.md)                     | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li><li><code>string _uri</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code> _properties</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleMetadata</code></a><code> _metadata</code></li><li><a href="../../data-structures/jboverflowallowance.md"><code>JBOverflowAllowance</code></a><code>[] _overflowAllowances</code></li><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>[] _payoutSplits</code></li><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>[] _reservedTokenSplits</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>_terminal</code></li></ul> |
| [**`reconfigureFundingCyclesOf`**](write/reconfigurefundingcyclesof.md) | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>_properties</code></li><li><a href="../../data-structures/jbfundingcyclemetadata.md"><code>JBFundingCycleMetadata</code></a><code> _metadata</code></li><li><p><a href="../../data-structures/jboverflowallowance.md"><code>JBOverflowAllowance</code></a><code>[] _overflowAllowances</code></p><p><code>JBSplit[] _payoutSplits</code></p></li><li><code>JBSplit[] _reservedTokenSplits</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 fundingCycleId</code></li></ul>                                                |
| [**`signalWithdrawalFrom`**](write/signalwithdrawalfrom.md)             | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>tappedFundingCycle</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| [**`mintTokensOf`**](write/minttokensof.md)                             | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li><li><a href="../jboperatable/modifiers/requirepermissionallowingoverride.md"><code>requirePermissionAllowingOverride</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li><li><code>address _beneficiary</code></li><li><code>string _memo</code></li><li><code>bool _preferClaimedTokens,</code></li><li><code>bool _shouldReserveTokens</code></li></ul>                                                                                                                                                                                                                                                                                                                           |
| [**`burnTokensOf`**](write/burntokensof.md)                             | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li><li><a href="../jboperatable/modifiers/requirepermissionallowingoverride.md"><code>requirePermissionAllowingOverride</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>address _holder</code></li><li><code>uint256 _projectId</code></li><li><code>uint256 _tokenCount</code></li><li><code>string _memo</code></li><li><code>bool _preferClaimedTokens</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                |
| [**`distributeReservedTokensOf`**](write/distributereservedtokensof.md) | <p><strong>Traits</strong></p><ul><li><code>nonReentrant</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>string _memo</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 tokenCount</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| [**`swapTerminalOf`**](write/swapterminalof.md)                         | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>_terminal</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| [**`prepForMigrationOf`**](write/prepformigrationof.md)                 | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>IJBController _from</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| [**`migrate`**](write/migrate.md)                                       | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li><li><code>nonReentrant</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>IJBTerminal _to</code></li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
