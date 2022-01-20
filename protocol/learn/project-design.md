---
description: >-
  A breakdown of how projects can design their treasury mechanism when building on the juicebox protocol. 
---

In order to understand what Juicebox can do for your project, all you have to do is understand how one transaction works: [`JBController.launchProjectFor(...)`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md).

```solidity
function launchProjectFor(
  address _owner,
  bytes32 _handle,
  string calldata _metadataCid,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
```

This transaction launches a project. The transaction executes the following steps to launch a project:
1. Mints a project in the `JBProjects` ERC-721 contract by calling [`JBProjects.createFor(...)`](../specifications/contracts/jbprojects/write/createfor.md).
1. Gives the `JBController` contract that is handling the `launchProjectFor` transaction (the currently executing transaction) authority to write to the `JBFundingCycleStore` and the `JBTokenStore` on the project's behalf. It does so by calling [`JBDirectory.setControllerOf(...)`](../specifications/contracts/jbdirectory/write/setcontrollerof.md).
1. Creates the project's first funding cycle using the provided `_data`, `_metadata`, and `_mustStartAtOrAfter` parameters. It does so by calling [`JBFundingCycleStore.configureFor(...)`](../specifications/contracts/jbfundingcyclestore/write/configurefor.md).
1. Stores splits for any provided split groups (`_groupedSplits`) by calling [`JBSplitStore.set(...)`](../specifications/contracts/jbsplitsstore/write/set.md).
1. Stores any provided constraints on how the project can access funds (`_fundAccessConstraints`) within any specified payment terminals (`_terminals`). It stores these constraints across [`JBController.distributionLimitOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/distributionlimitof.md), [`JBController.overflowAllowanceOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/overflowallowanceof.md), and [`JBController.currencyOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/currencyof.md).
1. Gives any provided terminals (`_terminals`) access to the `JBController` contract that is handling the `launchProjectFor` transaction (the currently executing transaction). Anyone or any other smart contract can check that the project is currently accepting funds through them by calling [`JBDirectory.terminalsOf(...)`](../specifications/contracts/jbdirectory/properties/terminalsof.md).


## Example project configurations

The following sections provide example parameters for the [`JBController.launchProjectFor(...)`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) function. Use these examples to learn how each parameter affects a project's behavior.

### Minimal project configuration

This is the most vanilla project you can launch. It incurs the least gas costs since it stores relatively little data.

* For `_data`, send the following [`JBFundingCycleData`](../specifications/data-structures/jbfundingcycledata.md) values:

  ```javascript
  {
    duration: 0,
    weight: 1000000000000000000000000,
    discountRate: 0,
    ballot: 0x0000000000000000000000000000000000000000
  }
  ```

* For `_metadata`, send the following [`JBFundingCycleMetadata`](../specifications/data-structures/jbfundingcyclemetadata.md) values:

  ```javascript
  {
    reservedRate: 0,
    redemptionRate: 0,
    ballotRedemptionRate: 0,
    pausePay: 0, 
    pauseDistributions: 0, 
    pauseRedeem: 0, 
    pauseMint: 0, 
    pauseBurn: 0, 
    allowTerminalMigration: 0, 
    allowControllerMigration: 0, 
    holdFees: 0, 
    useLocalBalanceForRedemptions: 0,
    useDataSourceForPay: 0, 
    useDataSourceForRedeem: 0, 
    dataSource: 0x0000000000000000000000000000000000000000, 
  }
  ```
* For `_groupedSplits`, send an empty array.
* For `_fundAccessConstraints`, send an empty array.
* For `_terminals`, send an array only including the contract address of the `JBETHPaymentTerminal`.

Under these conditions, your project has the following characteristics:
* Your project can begin receiving funds through the `JBETHPaymentTerminal`.
* 1,000,000 of your project's tokens will be minted per ETH received, because the configured `_data.weight` is `1000000000000000000000000` (the raw value sent has 18 decimal places).
* All tokens minted as a result of recieved ETH will go to the beneficiary address specified by the payer of the ETH, because the configured `_metadata.reservedRate` is 0.
* Nothing fancy will happen outside of the default token minting behavior because the configured `_metadata.useDataSourceForPay` is `false`. 
* None of the funds in the treasury can be distributed to the project owner because no `_fundAccessConstraints` were specified. This means all funds in the treasury are considered overflow. Because the configured `_metadata.redemptionRate` sent is 0 (which represents 100%), all outstanding tokens can be redeemed/burned to claim a proportional part of the overflow. This gives everyone who contributed funds the ability to access to their ETH.
* A new funding cycle with an updated configuration can be triggered at any time by the project owner because the configured `_data.duration` is 0 and `_data.ballot` is `0x0000000000000000000000000000000000000000`. This lets the project owner capture an arbitrary amount of what's in the treasury at any given point by sending a reconfiguration with `_fundAccessConstraints` specified.

Let's see what happens when basic `_fundAccessConstraints` are specified by sending the following [`JBFundAccessContraints`](../specifications/data-structures/jbfundaccessconstraints.md) values:

```javascript
[
  {
    terminal: <address of JBETHPaymentTerminal>,
    distributionLimit: 4200000000000000000,
    overflowAllowance: 0,
    distributionLimitCurrency: 0,
    overflowAllowanceCurrency: 0
  }
]
```

_wip_\
_to be continued_
