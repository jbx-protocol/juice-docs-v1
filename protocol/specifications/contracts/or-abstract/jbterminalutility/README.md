---
description: >-
  Provides tools for contracts that has functionality that can only be accessed
  by a project's terminals.
---

# JBTerminalUtility

## Overview

### Traits

`abstract`

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/abstract/JBTerminalUtility.sol" %}

### **Interfaces**

| Name                                                                  | Description                                                                                                                              |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBTerminalUtility`**](../../../interfaces/ijbterminalutility.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

## Constructor

```solidity
constructor(IJBDirectory _directory) {
  directory = _directory;
}
```

* **Arguments:**
  * `_directory` is an [`IJBDirectory`](../../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.

## Read

| Function                                   | Definition                                                                                                                                                                                                  |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`directory`**](properties/directory.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>IJBDirectory directory</code></li></ul><p><a href="properties/directory.md">more</a></p> |
