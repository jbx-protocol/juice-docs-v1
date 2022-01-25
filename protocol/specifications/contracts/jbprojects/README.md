---
description: >-
  Stores project ownership and identifying information.
---

# JBProjects

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.2/packages/hardhat/contracts/JBProjects.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\

### **Interfaces**

| Name                                                 | Description                                                                                                                              |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBProjects`**](../../interfaces/ijbprojects.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                                                     | Description                                                                                                           |
| ---------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| [**`ERC721Votes`**](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2873) | A checkpointable standard definition for non-fungible tokens (NFTs)                                                                  |
| [**`JBOperatable`**](../or-abstract/jboperatable/)                           | Includes convenience functionality for checking a message sender's permissions before executing certain transactions. |

## Constructor

```solidity
constructor(IJBOperatorStore _operatorStore)
  ERC721('Juicebox Projects', 'JUICEBOX')
  EIP712('Juicebox Projects', '1')
  JBOperatable(_operatorStore)
{}
```

* **Arguments:**
  * `_operatorStore` is an [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.

## Events

| Name                                                                                                      | Data                                                                                                                                                                                                                                 |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`Create`**](events/create.md)                                                                          | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed owner</code></li><li><code>bytes32 indexed handle</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul>                  |
| [**`SetMetadata`**](events/setmetadata.md) | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../../structs/jbprojectmetadata.md"><code>JBProjectMetadata<code><a><code>metadata</code></li><li><code>address caller</code></li></ul>                                                                                                         |
| [**`SetTokenUriResolver`**](events/settokenuriresolver.md) | <ul><li><a href="../../interfaces/ijbtokenuriresolver.md"><code>IJBTokenUriResolver<code></a><code>metadata</code></li><li><code>address caller</code></li></ul>                                                                                                         |

## Properties

| Name                                                                                                        | Definition                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`count`**](properties/count.md)                                                                          | <p><strong>Returns</strong></p><ul><li><code>uint256 count</code></li></ul>                                                                                                |
| [**`metadataContentOf`**](properties/metadatacontentof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _domain</code</li></ul><p><strong>Returns</strong></p><ul><li><code>string content</code></li></ul>                    |
| [**`tokenUriResolver`**](properties/tokenuriresolver.md) | <p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijbtokenuriresolver.md"><code>IJBTokenUriResolver</code></a><code>tokenUriResolver</code></li></ul>                    |

## Write

| Function                                                                                                     | Definition                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`createFor`**](write/createfor.md)                                                                        | <p><strong>Params</strong></p><ul><li><code>address _owner</code></li><li><code>bytes32 _handle</code></li><li><code>string _metadataCid</code></li><li><code>IJBTerminal _terminal</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 projectId</code></li></ul>                                             |
| [**`setMetadataOf`**](write/setmetadatacidof.md) | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>string _metadataCid</code></li></ul>                                                                                                                          |
| [**`setTokenUriResolver`**](write/settokenuriresolver.md) | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><a href="../../interfaces/ijbtokenuriresolver.md"><code>IJBTokenUriResolver</code></a><code>_projectId</code></li><li><code>string _metadataCid</code></li></ul>                                                                                                                          |
