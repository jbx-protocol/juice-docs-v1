---
description: Manages funding cycle configurations and scheduling.
---

# JBFundingCycleStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBFundingCycleStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\

### **Interfaces**

| Name                                                                   | Description                                                                                                                              |
| ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBFundingCycleStore`**](../../interfaces/ijbfundingcyclestore.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                                         | Description                                                                                                                                   |
| ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBControllerUtility`**](../or-abstract/jbcontrollerutility/) | Includes convenience functionality for checking if the message sender is the current controller of the project whose data is being manipulated.                                      |

## Constructor

```solidity
constructor(IJBDirectory _directory) JBControllerUtility(_directory) {}
```

* **Arguments:**
  * `_directory` is an [`IJBDirectory`](../../../../protocol/interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.

## Events

| Name                                   | Data                                                                                                                                                                                                                                                                                                                                                      |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Configure`**](events/configure.md) | <ul><li><code>uint256 indexed configuration</code></li><li><code>uint256 indexed projectId</code></li><code>[`JBFundingCycleData`](../../data-structures/jbfundingcycledata.md)data</code></li><li><code>uint256 metadata</code></li><li><code>uint256 mustStartAtOrAfter</code></li><li><code>address caller</code></li></ul> |
| [**`Init`**](events/init.md)           | <ul><li><code>uint256 indexed configuration</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed basedOn</code></li></ul>                                                                                                                                                                                                 |

## Properties

| Function                                     | Definition                                                                                                                                                    |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`latestConfigurationOf`**](properties/latestconfigurationof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 latestConfiguration</code></li></ul> |

## Read

| Function                                                   | Definition                                                                                                                                                                                                                                      |
| ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`get`**](read/get.md)                                   | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _configuration</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li></ul> |
| [**`queuedOf`**](read/queuedof.md)                         | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li></ul>      |
| [**`currentOf`**](read/currentof.md)                       | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBFundingCycle`](../../data-structures/jbfundingcycle.md)fundingCycle</code></li></ul>      |
| [**`currentBallotStateOf`**](read/currentballotstateof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`JBBallotState`](../../enums/jbballotstate.md)ballotState</code></li></ul>                   |

## Write

| Function                                    | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`configureFor`**](write/configurefor.md) | <p><strong>Traits</strong></p><ul><li><code>[`onlyController`](../../../../protocol/specifications/contracts/jbcontrollerutility/modifiers/onlycontroller.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`JBFundingCycleData`](../../../../protocol/data-structures/jbfundingcycledata.md)</code>_data</code></li><li><code>uint256 _metadata</code></li><li><code>uint256 _mustStartAtOrAfter</code></li></ul><p><strong>Returns</strong></p><ul><li><a href=""><code>[`JBFundingCycle`](../../../../protocol/data-structures/jbfundingcycle.md)fundingCycle</code></li></ul> |
