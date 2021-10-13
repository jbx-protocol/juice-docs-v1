---
description: >-
  Stores operator permissions for all addresses. Addresses can give permissions
  to any other address to take specific actions throughout the Juicebox
  ecosystem on their behalf.
---

# JBOperatorStore

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.2/packages/hardhat/contracts/JBOperatorStore.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

|                        |                                                                                                                                          |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Name**               | Description                                                                                                                              |
| **`IJBOperatorStore`** | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

## Events

| Name              | Data                                                                                                                                                                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **`SetOperator`** | <ul><li><code>address indexed operator</code></li><li><code>address indexed account</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256[] permissionIndexes</code></li><li><code>uint256 packed</code></li></ul><p><a href="events/setoperator.md">more</a></p> |

## Properties

| Function            | Definition                                                                                                                                                                                                                                                                                      |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`permissionsOf`** | <p><strong>Params</strong></p><ul><li><code>address _operator</code></li><li><code>address _account</code></li><li><code>uint256 _domain</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 permissions</code></li></ul><p><a href="properties/permissionsof.md">more</a></p> |

## Read

| Function             | Definition                                                                                                                                                                                                                                                                                                                                   |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`hasPermission`**  | <p><strong>Params</strong></p><ul><li><code>address _operator</code></li><li><code>address _account</code></li><li><code>uint256 _domain</code></li><li><code>uint256 _permissionIndex</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool hasPermission</code></li></ul><p><a href="read/haspermission.md">more</a></p>       |
| **`hasPermissions`** | <p><strong>Params</strong></p><ul><li><code>address _operator</code></li><li><code>address _account</code></li><li><code>uint256 _domain</code></li><li><code>uint256[] _permissionIndexes</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bool hasPermissions</code></li></ul><p><a href="read/haspermissions.md">more</a></p> |

## Write

| Function           | Definition                                                                                                                                                                                                    |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`setOperator`**  | <p><strong>Params</strong></p><ul><li><a href="../../data-structures/jboperatordata.md"><code>JBOperatorData</code></a><code>_operatorData</code></li></ul><p><a href="write/setoperator.md">more</a></p>     |
| **`setOperators`** | <p><strong>Params</strong></p><ul><li><a href="../../data-structures/jboperatordata.md"><code>JBOperatorData</code></a><code>[] _operatorData</code></li></ul><p><a href="write/setoperators.md">more</a></p> |
