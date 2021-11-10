---
description: >-
  A breakdown of how projects can design their treasury mechanism when building on the juicebox protocol. 
---

In order to understand what Juicebox can do, all you have to do is understand how one transaction works: [`JBController.launchProjectFor(...)`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md).

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
* Creating a project by minting a token in the `JBProjects` NFT contract by calling [`JBProjects.createFor(...)`](../specifications/contracts/jbprojects/write/createfor.md). 
* Then giving the `JBController` contract that is handling the `launchProjectFor` transaction that's currently being executed authority to write to the `JBFundingCycleStore` and the `JBTokenStore` on the project's behalf by calling [`JBDirectory.setControllerOf(...)`](../specifications/contracts/jbdirectory/write/setcontrollerof.md). 
* Then creating the project's first funding cycle using the provided `_data` and `_metadata` parameters by calling [`JBFundingCycleStore.configureFor(...)`](../specifications/contracts/jbfundingcyclestore/write/configurefor.md).
* Then storing splits for any provided split groups by calling [`JBSplitStore.set(...)`](../specifications/contracts/jbsplitsstore/write/set.md).
* Then storing any provided constraints on how the project will be able to access funds within any specified payment terminals by storing values in [`JBController.distributionLimitOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/distributionlimitof.md), [`JBController.overflowAllowanceOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/overflowallowanceof.md), and [`JBController.currencyOf(...)`](../specifications/contracts/or-controllers/jbcontroller/properties/currencyof.md).
* Then giving the provided `_terminals` access to the `JBController` contract that is handling the `launchProjectFor` transaction that's currently being executed, and also allowing anyone or any other contract in Web3 to know that the project is currently accepting funds through them by calling [`JBDirectory.setTerminalsOf(...)`](../specifications/contracts/jbdirectory/write/setterminalsof.md). 

Whew, that's a lot. Let's use an example, starting with the simplest one:

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
    useDataSourceForPay: 0, 
    useDataSourceForRedeem: 0, 
    dataSource: 0x0000000000000000000000000000000000000000, 
  }
  ```
* For `_groupedSplits` send an empty array.
* For `_fundAccessConstraints` send an empty array.
* For `_terminals` send an empty array with the contract address of the `JBETHPaymentTerminal`.
 

_wip_
_to be continued_
