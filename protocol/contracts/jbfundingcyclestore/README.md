---
description: Manage funding cycle configurations, ballots, accounting, and scheduling.
---

# JBFundingCycleStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBFundingCycleStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name                                                                   | Description                                                                                                                              |
| ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBFundingCycleStore`**](../../interfaces/ijbfundingcyclestore.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                             | Description                                                                                                                                   |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBControllerUtility`**](../jbcontrollerutility/) | Includes convenience functionality for checking if the message sender is the current controller of the project who data is being manipulated. |



## Constructor

```solidity
constructor(IJBDirectory _directory) JBControllerUtility(_directory) {}
```

* Arguments:
  * `_directory` is an [`IJBDirectory`](../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.

## Events

| Name                                   | Data                                                                                                                                                                                                                                                                                                                                                      |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Configure`**](events/configure.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 reconfigured</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>properties</code></li><li><code>uint256 metadata</code></li><li><code>address caller</code></li></ul> |
| [**`Tap`**](events/tap.md)             | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>uint256 newTappedAmount</code></li><li><code>address caller</code></li></ul>                                                                                                                         |
| [**`Init`**](events/init.md)           | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed basedOn</code></li></ul>                                                                                                                                                                                                 |

## Properties

| Function                                     | Definition                                                                                                                                                    |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`latestIdOf`**](properties/latestidof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 latestId</code></li></ul> |

## Read

| Function                                                   | Definition                                                                                                                                                                                                                                      |
| ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`get`**](read/get.md)                                   | <p><strong>Params</strong></p><ul><li><code>uint256 _fundingCycleId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul> |
| [**`queuedOf`**](read/queuedof.md)                         | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul>      |
| [**`currentOf`**](read/currentof.md)                       | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul>      |
| [**`currentBallotStateOf`**](read/currentballotstateof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../enums/jbballotstate.md"><code>JBBallotState</code></a><code>ballotState</code></li></ul>                   |

## Write

| Function                                    | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`configureFor`**](write/configurefor.md) | <p><strong>Traits</strong></p><ul><li><a href="../jbcontrollerutility/modifiers/onlycontroller.md"><code>onlyController</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>_properties</code></li><li><code>uint256 _metadata</code></li><li><code>uint256 _fee</code></li><li><code>bool _configureActiveFundingCycle</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="write/configurefor.md">more</a></p> |
| [**`tapFrom`**](write/tapfrom.md)           | <p><strong>Traits</strong></p><ul><li><a href="../jbcontrollerutility/modifiers/onlycontroller.md"><code>onlyController</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="write/tapfrom.md">more</a></p>                                                                                                                                                                                                                           |
