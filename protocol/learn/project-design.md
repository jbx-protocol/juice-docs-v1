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
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
```

This transaction launches a project. It does so by:
* Minting a project in the `JBProjects` NFT contract by calling [`JBProjects.createFor(...)`](../specifications/contracts/jbprojects/write/createfor.md). 
* Then giving the `JBController` contract that is handling the `launchProjectFor` transaction that's currently being executed authority to write to the `JBFundingCycleStore` and the `JBTokenStore` on the project's behalf by calling [`JBDirectory.setControllerOf(...)`](../specifications/contracts/jbdirectory/write/setcontrollerof.md). 
* Then creating the project's first funding cycle using the provided `_data` and `_metadata` parameters by calling [`JBFundingCycleStore.configureFor(...)`](../specifications/contracts/jbfundingcyclestore/write/configurefor.md).
* Then storing splits for any provided split groups by calling [`JBSplitStore.set(...)`](../specifications/contracts/jbsplitsstore/write/set.md).
* Then storing any provided constraints on how the project will be able to access funds within any specified payment terminals by storing values in [`JBController.distributionLimitOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/distributionlimitof.md), [`JBController.overflowAllowanceOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/overflowallowanceof.md), and [`JBController.currencyOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/currencyof.md).
* Then giving the provided `_terminals` access to the `JBController` contract that is handling the `launchProjectFor` transaction that's currently being executed, and also allowing anyone or any other contract in Web3 to know that the project is currently accepting funds through them by calling [`JBDirectory.setTerminalsOf(...)`](../specifications/contracts/jbdirectory/write/setterminalsof.md). 

Here are some examples, starting with the simplest version:

* For `_data` send the following [`JBFundingCycleMetadata`](../specifications/data-structures/jbfundingcyclemetadata.md) values:

  ```javascript
  {
    duration: 0,
    weight: 1000000000000000000000000,
    discountRate: 0,
    ballot: 0x0000000000000000000000000000000000000000
  }
  ```

* For `_metadata` send the following [`JBFundingCycleData`](../specifications/data-structures/jbfundingcycledata.md) values:

  ```javascript
  {
    reservedRate: 0,
    redemptionRate: 200,
    ballotRedemptionRate: 200,
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
* For `_groupedSplits` send an empty array.
* For `_fundAccessConstraints` send an empty array.
* For `_terminals` send an array only including the contract address of the `JBETHPaymentTerminal`.
 
This is the most vanilla project you can launch, which also makes it cheapest to launch gas-wise since relatively little needs to get saved into storage.

Under these conditions:
* Your project can begin receiving funds through the `JBETHPaymentTerminal`.
* 1,000,000 of your project's tokens will be minted per ETH received since the configured `_data.weight` of `1000000000000000000000000`. (The raw value sent has 18 decimal places).
* All tokens minted as a result of recieved ETH will go to the beneficiary address specified by the payer of the ETH since the configured `_metadata.reservedRate` of 0.
* Nothing fancy will happen outside of the default token minting behavior since the configured `_metadata.useDataSourceForPay` is `false`. 
* None of the funds in the treasury can be distributed to the project owner since no `_fundAccessConstraints` were specified. This means all funds in the treasury are considered overflow. Since the configured `_metadata.redemptionRate` is 200 (which represents 100%), all outstanding tokens can be redeemed/burned to claim a proportional part of the overflow. This lets everyone who contributed funds have access to their ETH back.
* A new funding cycle with an updated configuration can be triggered at any time by the project owner since the configured `_data.duration` of 0 and `_data.ballot` of `0x0000000000000000000000000000000000000000`. This lets the project owner capture an arbitrary amount of what's in the treasury at any given point by sending a reconfiguration with `_fundAccessConstraints` specified.

Let's see what happens when basic `_fundAccessConstraints` are specified by sending the following [`JBFundAccessContraints`](../specifications/data-structures/jbfundaccessconstraints.md) values:

```javascript
[
  {
    terminal: <address of JBETHPaymentTerminal>,
    distributionLimit: 4200000000000000000,
    overflowAllowance: 0,
    currency: 0
  }
]
```

_wip_\
_to be continued_
