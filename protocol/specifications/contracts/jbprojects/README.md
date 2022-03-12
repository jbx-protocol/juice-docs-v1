---
description: >-
  Stores project ownership and metadata.
---

# JBProjects

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.2/packages/hardhat/contracts/JBProjects.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_

### **Interfaces**

| Name                                                 | Description                                                                                                                              |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [**`IJBProjects`**](../../interfaces/ijbprojects.md) | General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules. |

### **Inheritance**

| Contract                                                                     | Description                                                                                                           |
| ---------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| [**`JBOperatable`**](../or-abstract/jboperatable/)                           | Includes convenience functionality for checking a message sender's permissions before executing certain transactions. |
| [**`ERC721Votes`**](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2873) | A checkpointable standard definition for non-fungible tokens (NFTs).                                                                  |
| [**`Ownable`**](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable) | Includes convenience functionality for specifying an address that owns the contract, with modifiers that only allow access by the owner. |

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
| [**`Create`**](events/create.md)                                                                          | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed owner</code></li><li><a href="../../../data-structures/jbprojectmetadata.md"><code>JBProjectMetadata</code></a><code>metadata</code></li><li><code>address caller</code></li></ul>                  |
| [**`SetMetadata`**](events/setmetadata.md) | <ul><li><code>uint256 indexed projectId</code></li><li><a href="../../../data-structures/jbprojectmetadata.md"><code>JBProjectMetadata<code><a><code>metadata</code></li><li><code>address caller</code></li></ul>                                                                                                         |
| [**`SetTokenUriResolver`**](events/settokenuriresolver.md) | <ul><li><a href="../../interfaces/ijbtokenuriresolver.md"><code>IJBTokenUriResolver<code></a><code>resolver</code></li><li><code>address caller</code></li></ul>                                                                                                         |

## Properties

| Name                                                                                                        | Definition                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`count`**](properties/count.md)                                                                          | <p><strong>Returns</strong></p><ul><li><code>uint256 count</code></li></ul>                                                                                                |
| [**`metadataContentOf`**](properties/metadatacontentof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>uint256 _domain</code</li></ul><p><strong>Returns</strong></p><ul><li><code>string content</code></li></ul>                    |
| [**`tokenUriResolver`**](properties/tokenuriresolver.md) | <p><strong>Returns</strong></p><ul><li><code>[`IJBTokenUriResolver`](../../interfaces/ijbtokenuriresolver.md)tokenUriResolver</code></li></ul>                    |

## Write

| Function                                                                                                     | Definition                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`createFor`**](write/createfor.md)                                                                        | <p><strong>Params</strong></p><ul><li><code>address _owner</code></li><li><code>[`JBProjectMetadata`](../../../data-structures/jbprojectmetadata.md)_metadata</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 projectId</code></li></ul>                                             |
| [**`setMetadataOf`**](write/setmetadataof.md) | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>[`JBProjectMetadata`](../../../data-structures/jbprojectmetadata.md)_metadata</code></li></ul>                                                                                                                          |
| [**`setTokenUriResolver`**](write/settokenuriresolver.md) | <p><strong>Traits</strong></p><ul><li><code>[`requirePermission`](../or-abstract/jboperatable/modifiers/requirepermission.md)</code></li></ul><p><strong>Params</strong></p><ul><li><code>[`IJBTokenUriResolver`](../../interfaces/ijbtokenuriresolver.md)_newResolver</code></li></ul>                                                                                                                          |
