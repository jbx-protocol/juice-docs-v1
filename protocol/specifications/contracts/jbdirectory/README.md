---
description: >-
  Keeps a reference of which terminal contracts each project is currently
  accepting funds through, and which controller contract is managing each
  project's tokens and funding cycles.
---

# JBDirectory

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts-v2/blob/main/contracts/JBDirectory.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\

### **Interfaces**

| Name                                                   | Description                                                                                                                              |
| ------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBDirectory`**](../../interfaces/ijbdirectory.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                               | Description                                                                                                                                                            |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`JBOperatable`**](../or-abstract/jboperatable/) | <p>Includes convenience functionality for checking a message sender's permissions before executing certain transactions.</p><p><a href="../or-abstract/jboperatable/">more</a></p> |

## Constructor

```solidity
constructor(IJBOperatorStore _operatorStore, IJBProjects _projects) JBOperatable(_operatorStore) {
  projects = _projects;
}
```

* **Arguments:**
  * `_operatorStore` is an [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.
  * `_projects` is an [`IJBProjects`](../../interfaces/ijbprojects.md) contract which mints ERC-721's that represent project ownership and transfers.

## Events

| Name                                                     | Data                                                                                                                                                                                                                                                |
| -------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetPrimaryTerminal`**](events/setprimaryterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed token</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul> |
| [**`AddTerminal`**](events/addterminal.md)               | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul>                                            |
| [**`RemoveTerminal`**](events/removeterminal.md)         | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>indexed terminal</code></li><li><code>address caller</code></li></ul>                                            |
| [**`SetController`**](events/setcontroller.md)           | <ul><li><code>int256 indexed projectId</code></li><li><a href="../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>indexed controller</code></li><li><code>address caller</code></li></ul>                                       |
| [**`AddToSetControllerAllowlist`**](events/addtosetcontrollerallowlist.md) | <ul><li><code>address indexed controller</code></li><li><code>address caller</code></li></ul> |
| [**`RemoveFromSetControllerAllowlist`**](events/addtosetcontrollerallowlist.md) | <ul><li><code>address indexed controller</code></li><li><code>address caller</code></li></ul> |

## Properties

| Function                                                                                   | Definition                                                                                                                                                                                                         |
| ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`projects`**](../or-payment-terminals/jbethpaymentterminalstore/properties/projects.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbprojects.md"><code>IJBProjects</code></a><code>projects</code></li></ul> |
| [**`controllerOf`**](properties/controllerof.md)                                           | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>controllerOf</code></li></ul> |

## Read

| Function                                                   | Definition                                                                                                                                                                                                                                                      |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`terminalsOf`**](read/terminalsof.md)                   | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>[] terminals</code></li></ul>                                 |
| [**`isTerminalOf`**](read/isterminalof.md)                 | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool isTerminal</code></li></ul>                                                                                                    |
| [**`isTerminalDelegateOf`**](read/isterminaldelegateof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool isTerminalDelegate</code></li></ul>                                                                                            |
| [**`primaryTerminalOf`**](read/primaryterminalof.md)       | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>address _token</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>terminal</code></li></ul> |

## Write

| Function                                                    | Definition                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`setControllerOf`**](write/setcontrollerof.md)           | <p><strong>Traits</strong></p><ul><li><a href="../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md"><code>requirePermissionAllowingOverride</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../interfaces/ijbcontroller.md"><code>IJBController</code></a><code>_controller</code></li></ul> |
| [**`addTerminalsOf`**](write/addterminalsof.md)               | <p><strong>Traits</strong></p><ul><li><a href="../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md"><code>requirePermissionAllowingOverride</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>[] _terminal</code></li></ul>       |
| [**`removeTerminalOf`**](write/removeterminalof.md)         | <p><strong>Traits</strong></p><ul><li><a href="../or-abstract/jboperatable/modifiers/requirepermission.md"><code>requirePermission</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>_terminal</code></li></ul>                                       |
| [**`setPrimaryTerminalOf`**](write/setprimaryterminalof.md) | <p><strong>Traits</strong></p><ul><li><a href="../or-abstract/jboperatable/modifiers/requirepermission.md"><code>requirePermission</code></a></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><a href="../../interfaces/ijbterminal.md"><code>IJBTerminal</code></a><code>_terminal</code></li></ul>                                       |
| [**`addToSetControllerAllowList`**](write/addtosetcontrollerallowlist.md) | <p><strong>Traits</strong></p><ul><li><code>onlyOwner</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _controller</code></li></ul>                                       |
| [**`removeFromSetControllerAllowList`**](write/removefromsetcontrollerallowlist.md) | <p><strong>Traits</strong></p><ul><li><code>onlyOwner</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _controller</code></li></ul>                                       |
