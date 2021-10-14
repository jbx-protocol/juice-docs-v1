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

## Events

| Name            | Data                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`Configure`** | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 reconfigured</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>properties</code></li><li><code>uint256 metadata</code></li><li><code>address caller</code></li></ul><p><a href="events/configure.md">more</a></p> |
| **`Tap`**       | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>uint256 newTappedAmount</code></li><li><code>address caller</code></li></ul><p><a href="events/tap.md">more</a></p>                                                                                                                               |
| **`Init`**      | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed basedOn</code></li></ul><p><a href="events/init.md">more</a></p>                                                                                                                                                                                                      |

## Properties

| Function              | Definition                                                                                                                                                                                                      |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`MAX_CYCLE_LIMIT`** | <p><strong>Traits</strong></p><ul><li><code>constant</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 32</code></li></ul><p><a href="./">more</a></p>                                       |
| **`latestIdOf`**      | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 latestId</code></li></ul><p><a href="properties/latestidof.md">more</a></p> |

## Read

| Function                   | Definition                                                                                                                                                                                                                                                                            |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`get`**                  | <p><strong>Params</strong></p><ul><li><code>uint256 _fundingCycleId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="read/get.md">more</a></p>  |
| **`queuedOf`**             | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="read/queuedof.md">more</a></p>  |
| **`currentOf`**            | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="read/currentof.md">more</a></p> |
| **`currentBallotStateOf`** | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../enums/jbballotstate.md"><code>JBBallotState</code></a><code>ballotState</code></li></ul><p><a href="read/currentballotstateof.md">more</a></p>   |

## Write

| Function           | Definition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`configureFor`** | <p><strong>Traits</strong></p><ul><li><a href="../jbcontrollerutility/modifiers/onlycontroller.md"><code>onlyController</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../data-structures/jbfundingcycledata.md"><code>JBFundingCycleData</code></a><code>_properties</code></li><li><code>uint256 _metadata</code></li><li><code>uint256 _fee</code></li><li><code>bool _configureActiveFundingCycle</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="write/configurefor.md">more</a></p> |
| **`tapFrom`**      | <p><strong>Traits</strong></p><ul><li><a href="../jbcontrollerutility/modifiers/onlycontroller.md"><code>onlyController</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbfundingcycle.md"><code>JBFundingCycle</code></a><code>fundingCycle</code></li></ul><p><a href="write/tapfrom.md">more</a></p>                                                                                                                                                                                                                           |
