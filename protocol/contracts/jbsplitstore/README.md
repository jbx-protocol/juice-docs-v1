---
description: Stores splits information for all groups of each project.
---

# JBSplitStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBSplitStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

|                     |                                                                                                                                          |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**            | Description                                                                                                                              |
| **`IJBSplitStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

|                    |                                                                                                                                                                        |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**           | Description                                                                                                                                                            |
| **`JBOperatable`** | <p>Includes convenience functionality for checking a message sender's permissions before executing certain transactions.</p><p><a href="../jboperatable/">more</a></p> |

## Constructor

```solidity
/** 
  @param _operatorStore A contract storing operator assignments.
  @param _jbDirectory The directory of terminals.
  @param _projects A Projects contract which mints ERC-721's that represent project ownership and transfers.
*/
constructor(
  IJBOperatorStore _operatorStore,
  IJBDirectory _directory,
  IJBProjects _projects
) JBOperatable(_operatorStore) {
  projects = _projects;
  directory = _directory;
}
```

## Events

|                |                                                                                                                                                                                                                                                                                                                               |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**       | Data                                                                                                                                                                                                                                                                                                                          |
| **`SetSplit`** | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>split</code></li><li><code>address caller</code></li></ul><p><a href="events/setsplit.md">more</a></p> |

## Properties

| Function        | Definition                                                                                                                                                                                   |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`projects`**  | <p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbprojects.md"><code>IJBProjects</code></a><code>projects</code></li></ul><p><a href="properties/projects.md">more</a></p> |
| **`directory`** | <p>Returns</p><ul><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>terminal</code></li></ul><p><a href="properties/directory.md">more</a></p>                 |

## Read

| Function       | Definition                                                                                                                                                                                                                                                                                                                                   |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`splitsOf`** | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _domain</code></li><li><code>uint256 _group</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>[] splits</code></li></ul><p><a href="read/splitsof.md">more</a></p> |

## Write

| Function  | Definition                                                                                                                                                                                                                                                                                                                                                                             |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`set`** | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _configuration</code></li><li><code>uint256 _group</code></li><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>[] _splits</code></li></ul><p><a href="write/set.md">more</a></p> |
