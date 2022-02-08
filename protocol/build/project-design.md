---
description: >-
  A breakdown of how projects can design their treasury mechanism when building
  on the juicebox protocol.
---

# Project design

In order to understand what Juicebox can do for your project, all you have to do is understand how one transaction works: [`JBController.launchProjectFor(...)`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md).

```solidity
function launchProjectFor(
  address _owner,
  JBProjectMetadata calldata _projectMetadata,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
 { ... }
```

This transaction launches a project. It does so by:

* Minting a project in the [`JBProjects`](../specifications/contracts/jbprojects/) ERC-721 contract by calling [`JBProjects.createFor(...)`](../specifications/contracts/jbprojects/write/createfor.md).
* Then giving the [`JBController`](../specifications/contracts/or-controllers/jbcontroller/) contract that is handling the [`launchProjectFor`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) transaction that's currently being executed authority to write to the [`JBFundingCycleStore`](../specifications/contracts/jbfundingcyclestore/) and the [`JBTokenStore`](../specifications/contracts/jbtokenstore/) on the project's behalf by calling [`JBDirectory.setControllerOf(...)`](../specifications/contracts/jbdirectory/write/setcontrollerof.md).
* Then creating the project's first funding cycle using the provided `_data`, `_metadata`, and `_mustStartAtOrAfter` parameters by calling [`JBFundingCycleStore.configureFor(...)`](../specifications/contracts/jbfundingcyclestore/write/configurefor.md).
* Then storing splits for any provided split groups by calling [`JBSplitStore.set(...)`](../specifications/contracts/jbsplitsstore/write/set.md).
* Then storing any provided constraints on how the project will be able to access funds within any specified payment terminals by storing values in [`JBController._packedDistributionLimitDataOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/\_packeddistributionlimitdataof.md), [`JBController._packedOverflowAllowanceDataOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/\_packedoverflowallowancedataof.md).
* Then giving the provided `_terminals` access to the [`JBController`](../specifications/contracts/or-controllers/jbcontroller/) contract that is handling the [`launchProjectFor`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) transaction that's currently being executed, and also allowing anyone or any other contract in Web3 to know that the project is currently accepting funds through them by calling [`JBDirectory.setTerminalsOf(...)`](../specifications/contracts/jbdirectory/write/setterminalsof.md).

Here are some examples, starting with the simplest version:

*   For `_data` send the following [`JBFundingCycleData`](../specifications/data-structures/jbfundingcycledata.md) values:

    ```javascript
    {
      duration: 0,
      weight: 1000000000000000000000000,
      discountRate: 0,
      ballot: 0x0000000000000000000000000000000000000000
    }
    ```
*   For `_metadata` send the following [`JBFundingCycleMetadata`](../specifications/data-structures/jbfundingcyclemetadata.md) values:

    ```javascript
    {
      reservedRate: 0,
      redemptionRate: 0,
      ballotRedemptionRate: 0,
      pausePay: false, 
      pauseDistributions: false, 
      pauseRedeem: false, 
      pauseMint: false, 
      pauseBurn: false, 
      allowTerminalMigration: false, 
      allowControllerMigration: false, 
      holdFees: false, 
      useLocalBalanceForRedemptions: false,
      useDataSourceForPay: false, 
      useDataSourceForRedeem: false, 
      dataSource: 0x0000000000000000000000000000000000000000, 
    }
    ```
* For `_groupedSplits` send an empty array.
* For `_fundAccessConstraints` send an empty array.
* For `_terminals` send an array only including the contract address of the [`JBETHPaymentTerminal`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/).

This is the most vanilla project you can launch, which also makes it cheapest to launch gas-wise since relatively little needs to get saved into storage.

Under these conditions:

* Your project can begin receiving funds through the [`JBETHPaymentTerminal`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/).
* 1,000,000 of your project's tokens will be minted per ETH received since the configured `_data.weight` is `1000000000000000000000000`. (The raw value sent has 18 decimal places).
* All tokens minted as a result of received ETH will go to the beneficiary address specified by the payer of the ETH since the configured `_metadata.reservedRate` of 0.
* Nothing fancy will happen outside of the default token minting behavior since the configured `_metadata.useDataSourceForPay` is `false`.
* Nothing fancy will happen outside of the default token redemption behavior since the configured `_metadata.useDataSourceForRedeem` is `false`.
* None of the funds in the treasury can be distributed to the project owner since no `_fundAccessConstraints` were specified. This means all funds in the treasury are considered overflow. Since the configured `_metadata.redemptionRate` sent is 0 (which represents 100%), all outstanding tokens can be redeemed/burned to claim a proportional part of the overflow. This lets everyone who contributed funds have access to their ETH back.
* A new funding cycle with an updated configuration can be triggered at any time by the project owner since the configured `_data.duration` of 0 and `_data.ballot` of `0x0000000000000000000000000000000000000000`. This lets the project owner capture an arbitrary amount of what's in the treasury at any given point by sending a reconfiguration transaction with `_fundAccessConstraints` specified.

Here's what happens when basic `_fundAccessConstraints` are specified by sending the following [`JBFundAccessContraints`](../specifications/data-structures/jbfundaccessconstraints.md) values:

```javascript
[
  {
    terminal: <address of JBETHPaymentTerminal>,
    distributionLimit: 4200000000000000000,
    overflowAllowance: 0,
    distributionLimitCurrency: 1,
    overflowAllowanceCurrency: 0
  }
]
```

* During each funding cycle with this configuration, the project can receive up to 4.2 ETH worth of tokens from the [`JBETHPaymentTerminal`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/), since the configured `distributionLimitCurrency` is 1 ([which represents ETH](../specifications/libraries/jbcurrencies.md)) and the `distributionLimit` is `4200000000000000000`. (The raw value sent has 18 decimal places).
* Anyone on the internet can call the [`JBETHPaymentTerminal.distributePayoutsOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/distributepayoutsof.md) transaction to send up to 4.2 ETH per funding cycle to the preconfigured splits. Since no splits were specified, all distributed funds go to the project owner.
* With each new funding cycle, another 4.2 ETH can be distributed.&#x20;
* The project cannot distribute any funds in excess of the distribution limit wince there is no `overflowAllowance`.

Here's what happens when using an overflow allowance instead:

```solidity
[
  {
    terminal: <address of JBETHPaymentTerminal>,
    distributionLimit: 0,
    overflowAllowance: 6900000000000000000,
    distributionLimitCurrency: 0,
    overflowAllowanceCurrency: 1
  }
]
```

* Until a new reconfiguration transaction is sent, the project owner can send up to 6.9 ETH worth of tokens from the [`JBETHPaymentTerminal`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/) to any address it chooses since the configured `overflowAllowanceCurrency` is 1 ([which represents ETH](../specifications/libraries/jbcurrencies.md)) and the `overflowAllowance` is `6900000000000000000`. (The raw value sent has 18 decimal places).
* Meanwhile, all of the project's funds in the [`JBETHPaymentTerminal`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/) are considered overflow since there is no distribution limit.
* Rolled-over funding cycles within the same configuration do not refresh the allowance.
* An overflow allowance is a free allowance the project can to use without additional pre-programmed stipulations.&#x20;
