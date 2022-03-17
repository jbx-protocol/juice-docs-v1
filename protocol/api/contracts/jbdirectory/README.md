---
description: >-
  Keeps a reference of which terminal contracts each project is currently accepting funds through, and which controller contract is managing each project's tokens and funding cycles.
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
| [**`JBOperatable`**](../or-abstract/jboperatable/) | Includes convenience functionality for checking a message sender's permissions before executing certain transactions. |
| [**`Ownable`**](https://docs.openzeppelin.com/contracts/4.x/api/security) | Includes convenience functionality for checking a message sender's permissions before executing certain transactions. |

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
| [**`AddTerminal`**](events/addterminal.md)               | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)indexed terminal</code></li><li><code>address caller</code></li></ul>                                            |
| [**`RemoveTerminal`**](events/removeterminal.md)         | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)indexed terminal</code></li><li><code>address caller</code></li></ul>                                            |
| [**`SetPrimaryTerminal`**](events/setprimaryterminal.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed token</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)indexed terminal</code></li><li><code>address caller</code></li></ul> |
| [**`SetController`**](events/setcontroller.md)           | <ul><li><code>int256 indexed projectId</code></li><li><code>[`IJBController`](../../interfaces/ijbcontroller.md)indexed controller</code></li><li><code>address caller</code></li></ul>                                       |
| [**`AddToSetControllerAllowlist`**](events/addtosetcontrollerallowlist.md) | <ul><li><code>address indexed allowedAddress</code></li><li><code>address caller</code></li></ul> |
| [**`RemoveFromSetControllerAllowlist`**](events/addtosetcontrollerallowlist.md) | <ul><li><code>address indexed removedAddress</code></li><li><code>address caller</code></li></ul> |

## Properties

| Function                                                                                   | Definition                                                                                                                                                                                                         |
| ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`projects`**](../or-payment-terminals/jbethpaymentterminalstore/properties/projects.md) | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBProjects`](../../interfaces/ijbprojects.md)projects</code></li></ul> |
| [**`controllerOf`**](properties/controllerof.md)                                           | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBController`](../../interfaces/ijbcontroller.md)controllerOf</code></li></ul> |
| [**`isAllowedToSetController`**](properties/isallowedtosetcontroller.md)                                           | <p><strong>Params</strong></p><ul><li><code>address _controller</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool isAllowedToSetController</code></li></ul> |

## Read

| Function                                                   | Definition                                                                                                                                                                                                                                                      |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`terminalsOf`**](read/terminalsof.md)                   | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)[] terminals</code></li></ul>                                 |
| [**`isTerminalOf`**](read/isterminalof.md)                 | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)terminal</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool isTerminal</code></li></ul>                                                                                                    |
| [**`primaryTerminalOf`**](read/primaryterminalof.md)       | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>address _token</code></li></ul><p><strong>Returns</strong></p><ul><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)terminal</code></li></ul> |

## Write

| Function                                                    | Definition                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`addTerminalsOf`**](write/addterminalsof.md)               | <p><strong>Traits</strong></p><ul><li><code>[`requirePermissionAllowingOverride`](../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)[] _terminals</code></li></ul>       |
| [**`removeTerminalOf`**](write/removeterminalof.md)         | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)_terminal</code></li></ul>                                       |
| [**`setControllerOf`**](write/setcontrollerof.md)           | <p><strong>Traits</strong></p><ul><li><code>[`requirePermissionAllowingOverride`](../or-abstract/jboperatable/modifiers/requirepermissionallowingoverride.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`IJBController`](../../interfaces/ijbcontroller.md)_controller</code></li></ul> |
| [**`setPrimaryTerminalOf`**](write/setprimaryterminalof.md) | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`IJBTerminal`](../../interfaces/ijbterminal.md)_terminal</code></li></ul>                                       |
| [**`addToSetControllerAllowList`**](write/addtosetcontrollerallowlist.md) | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _address</code></li></ul>                                       |
| [**`removeFromSetControllerAllowList`**](write/removefromsetcontrollerallowlist.md) | <p><strong>Traits</strong></p><ul><li><code>[`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--)</code></li></ul><p><strong>Params</strong></p><ul><li><code>address _address</code></li></ul>                                       |
