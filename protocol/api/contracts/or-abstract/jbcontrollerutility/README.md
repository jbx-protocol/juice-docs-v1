---
description: >-
  Provides tools for contracts that has functionality that can only be accessed
  by a project's controller.
---

# JBControllerUtility

## Overview

### Traits

`abstract`

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/abstract/JBControllerUtility.sol" %}

### **Interfaces**

| Name                                                                      | Description                                                                                                                              |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBControllerUtility`**](../../../interfaces/ijbcontrollerutility.md) | General interface for the methods in this contract that interact with the blockchain's state according to the protocol's rules. |

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
