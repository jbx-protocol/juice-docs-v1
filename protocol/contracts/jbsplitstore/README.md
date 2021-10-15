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

### **Inheritance**

## Constructor

```solidity
constructor(
  IJBOperatorStore _operatorStore,
  IJBProjects _projects,
  IJBDirectory _directory
) JBOperatable(_operatorStore) {
  projects = _projects;
  directory = _directory;
}
```

* **Arguments:**
  * `_operatorStore` is an [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.
  * `_projects` is an [`IJBProjects`](../../interfaces/ijbprojects.md) contract which mints ERC-721's that represent project ownership and transfers.
  * `_directory` is an [`IJBDirectory`](../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.

## Events

| Name                                 | Data                                                                                                                                                                                                                 |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetSplit`**](events/setsplit.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><code>Split split</code></li><li><code>address caller</code></li></ul> |

## Properties

| Function                                   | Definition                                                                         |
| ------------------------------------------ | ---------------------------------------------------------------------------------- |
| [**`projects`**](properties/projects.md)   | <p><strong>Returns</strong></p><ul><li><code>IJBProjects projects</code></li></ul> |
| [**`directory`**](properties/directory.md) | <p><strong>Returns</strong></p><ul><li><code>IJBTerminal terminal</code></li></ul> |

## Read

| Function                           | Definition                                                                                                                                                                                                                                                                                         |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`splitsOf`**](read/splitsof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _domain</code></li><li><code>uint256 _group</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>[] splits</code></li></ul> |

## Write

| Function                  | Definition                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`set`**](write/set.md) | <p><strong>Traits</strong></p><ul><li><code>requirePermissionAllowingOverride</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _configuration</code></li><li><code>uint256 _group</code></li><li><a href="../../data-structures/jbsplit.md"><code>JBSplit</code></a><code>[] _splits</code></li></ul> |
