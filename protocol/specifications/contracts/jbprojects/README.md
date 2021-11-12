---
description: >-
  Manages ownership over projects, which are represented as ERC-721 tokens.
  Project's can record a URI where some front-end metadata is stored, and
  reserve a handle for reverse ID lookup.
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
| [**`ERC721`**](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) | A standard definition for non-fungible tokens (NFTs)                                                                  |
| [**`JBOperatable`**](../or-abstract/jboperatable/)                           | Includes convenience functionality for checking a message sender's permissions before executing certain transactions. |

## Constructor

```solidity
constructor(IJBOperatorStore _operatorStore)
  ERC721('Juicebox project', 'JUICEBOX')
  JBOperatable(_operatorStore)
{}
```

* **Arguments:**
  * `_operatorStore` is an [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.

## Events

| Name                                                                                                      | Data                                                                                                                                                                                                                                 |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`Create`**](events/create.md)                                                                          | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed owner</code></li><li><code>bytes32 indexed handle</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul>                  |
| [**`SetHandle`**](events/sethandle.md)                                                                    | <ul><li><code>uint256 indexed projectId</code></li><li><code>bytes32 indexed handle</code></li><li><code>address caller</code></li></ul>                                                                                             |
| [**`SetMetadataCid`**](../../../../protocol/specifications/contracts/jbprojects/events/setmetadatacid.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul>                                                                                                         |
| [**`TransferHandle`**](events/transferhandle.md)                                                          | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed transferAddress</code></li><li><code>bytes32 indexed handle</code></li><li><code>bytes32 newHandle</code></li><li><code>address caller</code></li></ul> |
| [**`ClaimHandle`**](events/claimhandle.md)                                                                | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed transferAddress</code></li><li><code>bytes32 indexed handle</code></li><li><code>address caller</code></li></ul>                                        |
| [**`ChallengeHandle`**](events/challengehandle.md)                                                        | <ul><li><code>bytes32 indexed handle</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 challengeExpiry</code></li><li><code>address caller</code></li></ul>                                                |
| [**`RenewHandle`**](events/renewhandle.md)                                                                | <ul><li><code>bytes32 indexed handle</code></li><li><code>uint256 indexed projectId</code></li><li><code>address caller</code></li></ul>                                                                                             |

## Properties

| Name                                                                                                        | Definition                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`count`**](properties/count.md)                                                                          | <p><strong>Returns</strong></p><ul><li><code>uint256 count</code></li></ul>                                                                                                |
| [**`metadataCidOf`**](../../../../protocol/specifications/contracts/jbprojects/properties/metadatacidof.md) | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>string uri</code></li></ul>                    |
| [**`handleOf`**](properties/handleof.md)                                                                    | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bytes32 handle</code></li></ul>                |
| [**`idFor`**](properties/idfor.md)                                                                          | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 projectId</code></li></ul>                |
| [**`transferAddressFor`**](properties/transferaddressfor.md)                                                | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>address transferAddress</code></li></ul>          |
| [**`challengeExpiryOf`**](properties/challengeexpiryof.md)                                                  | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 challengeExpiryTimestamp</code></li></ul> |

## Write

| Function                                                                                                     | Definition                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`createFor`**](write/createfor.md)                                                                        | <p><strong>Params</strong></p><ul><li><code>address _owner</code></li><li><code>bytes32 _handle</code></li><li><code>string _metadataCid</code></li><li><code>IJBTerminal _terminal</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 projectId</code></li></ul>                                             |
| [**`setHandleOf`**](write/sethandleof.md)                                                                    | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>bytes32 _handle</code></li></ul>                                                                                                                              |
| [**`setMetadataCidOf`**](../../../../protocol/specifications/contracts/jbprojects/write/setmetadatacidof.md) | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>string _metadataCid</code></li></ul>                                                                                                                          |
| [**`transferHandleOf`**](write/transferhandleof.md)                                                          | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>address _transferAddress</code></li><li><code>bytes32 _newHandle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bytes32 handle</code></li></ul> |
| [**`claimHandle`**](write/claimhandle.md)                                                                    | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li><li><code>address _for</code></li><li><code>uint256 _projectId</code></li></ul>                                                                                            |
| [**`challengeHandle`**](write/challengehandle.md)                                                            | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul>                                                                                                                                                                                                                                                    |
| [**`renewHandleOf`**](write/renewhandleof.md)                                                                | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul>                                                                                                                                                                   |
