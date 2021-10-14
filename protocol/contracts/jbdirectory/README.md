---
description: >-
  Keeps a reference of which payment terminal contract each project is currently
  accepting funds through. When a project migrates from one terminal to another,
  the change is reflected in this contract.
---

# JBDirectory

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBDirectory.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name                                                   | Description                                                                                                                              |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBDirectory`**](../../interfaces/ijbdirectory.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                               | Description                                                                                                                                                            |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBOperatable`**](../jboperatable/) | <p>Includes convenience functionality for checking a message sender's permissions before executing certain transactions.</p><p><a href="../jboperatable/">more</a></p> |

## Events

| Name                                       | Data                                                                                                                                                                                          |
| ------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetTerminal`**](events/setterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>IJBTerminal indexed terminal</code></li><li><code>address caller</code></li></ul><p><a href="events/setterminal.md">more</a></p> |

## Read

| Function         | Definition                                                                                                                                                                                                      |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`projects`**   | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>IJBProjects projects</code></li></ul><p><a href="./">more</a></p>                            |
| **`terminalOf`** | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>IJBTerminal terminalOf</code></li></ul><p><a href="read/terminalof.md">more</a></p> |

## Write

| Function            | Definition                                                                                                                                                                 |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`setTerminalOf`** | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>IJBTerminal _terminal</code></li></ul><p><a href="write/setterminalof.md">more</a></p> |
