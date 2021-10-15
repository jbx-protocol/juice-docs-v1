---
description: Stores ETH.
---

# JBETHVault

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2/JBETHVault.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name                                                   | Description                                                                                                                              |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBVault`**](../../../interfaces/ijbtokenstore.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                                        | Description                                                                                                                               |
| --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBTerminalUtility`**](../../or-abstract/jbterminalutility/) | Includes convenience functionality for checking if the message sender is a current terminal of the project who data is being manipulated. |

## Constructor

```solidity
constructor(IJBDirectory _directory) JBTerminalUtility(_directory) {}
```

* **Arguments:**
  * `_directory` is an [`IJBDirectory`](../../../interfaces/ijbdirectory.md) contract storing directories of terminals and controllers for each project.

## Events

## Read

| Name                                 | Data                                                                                                                                                             |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Withdraw`**](events/withdraw.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>address to</code></li><li><code>address caller</code></li></ul> |
| [**`Deposit`**](events/deposit.md)   | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>address caller</code></li></ul>                                 |

## Read

| Function                     | Definition                                                                  |
| ---------------------------- | --------------------------------------------------------------------------- |
| [**`token`**](read/token.md) | <p><strong>Returns</strong></p><ul><li><code>address token</code></li></ul> |

## Write

| Function                            | Definition                                                                                                                                                                                                                     |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`deposit`**](write/deposit.md)   | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li></ul>                                  |
| [**`withdraw`**](write/withdraw.md) | <p><strong>Traits</strong></p><ul><li><code>onlyTerminal</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _amount</code></li><li><code>address _to</code></li></ul> |
