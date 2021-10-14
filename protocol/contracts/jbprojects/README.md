---
description: Manages ownership over projects, which are represented as ERC-721 tokens.
---

# JBProjects

## Overview

### Code

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.2/packages/hardhat/contracts/JBProjects.sol" %}

### **Addresses**

Ethereum mainnet: _Not yet deployed_\
Rinkeby testnet: _Not yet deployed_

### **Interfaces**

| Name              | Description                                                                                                                                                                                              |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`IJBProjects`** | <p>General interface for the methods in this contract that interact with the blockchain's state according to the Juicebox protocol's rules.</p><p><a href="../../interfaces/ijbprojects.md">more</a></p> |

### **Inheritance**

| Contract           | Description                                                                                                                                                            |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`ERC721`**       | A standard definition for non-fungible tokens (NFTs)                                                                                                                   |
| **`JBOperatable`** | <p>Includes convenience functionality for checking a message sender's permissions before executing certain transactions.</p><p><a href="../jboperatable/">more</a></p> |

## Constructor

```solidity
/** 
  @param _operatorStore A contract storing operator assignments.
*/
constructor(IJBOperatorStore _operatorStore)
  ERC721('Juicebox project', 'JUICEBOX')
  JBOperatable(_operatorStore)
{}
```

* Arguments:
  * `_operatorStore` A [`IJBOperatorStore`](../../interfaces/ijboperatorstore.md) contract storing operator assignments.

## Events

| Name                  | Data                                                                                                                                                                                                                                                                                   |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`Create`**          | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed owner</code></li><li><code>bytes32 indexed handle</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul><p><a href="events/create.md">more</a></p>                          |
| **`SetHandle`**       | <ul><li><code>uint256 indexed projectId</code></li><li><code>bytes32 indexed handle</code></li><li><code>address caller</code></li></ul><p><a href="events/sethandle.md">more</a></p>                                                                                                  |
| **`SetUri`**          | <ul><li><code>uint256 indexed projectId</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul><p><a href="events/seturi.md">more</a></p>                                                                                                                 |
| **`TransferHandle`**  | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed transferAddress</code></li><li><code>bytes32 indexed handle</code></li><li><code>bytes32 newHandle</code></li><li><code>address caller</code></li></ul><p><a href="events/transferhandle.md">more</a></p> |
| **`ClaimHandle`**     | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed transferAddress</code></li><li><code>bytes32 indexed handle</code></li><li><code>address caller</code></li></ul><p><a href="events/claimhandle.md">more</a></p>                                           |
| **`ChallengeHandle`** | <ul><li><code>bytes32 indexed handle</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 challengeExpiry</code></li><li><code>address caller</code></li></ul><p><a href="events/challengehandle.md">more</a></p>                                               |
| **`RenewHandle`**     | <ul><li><code>bytes32 indexed handle</code></li><li><code>uint256 indexed projectId</code></li><li><code>address caller</code></li></ul><p><a href="events/renewhandle.md">more</a></p>                                                                                                |

## Properties

| Name                     | Definition                                                                                                                                                                                                                          |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`count`**              | <p><strong>Returns</strong></p><ul><li><code>uint256 count</code></li></ul><p><a href="properties/count.md">more</a></p>                                                                                                            |
| **`uriOf`**              | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>string uri</code></li></ul><p><a href="properties/uriof.md">more</a></p>                                |
| **`handleOf`**           | <p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bytes32 handle</code></li></ul><p><a href="properties/handleof.md">more</a></p>                         |
| **`idFor`**              | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 projectId</code></li></ul><p><a href="properties/idfor.md">more</a></p>                            |
| **`transferAddressFor`** | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>address transferAddress</code></li></ul><p><a href="properties/transferaddressfor.md">more</a></p>         |
| **`challengeExpiryOf`**  | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 challengeExpiryTimestamp</code></li></ul><p><a href="properties/challengeexpiryof.md">more</a></p> |

## Write

| Function               | Definition                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`createFor`**        | <p><strong>Params</strong></p><ul><li><code>address _owner</code></li><li><code>bytes32 _handle</code></li><li><code>string _uri</code></li><li><code>IJBTerminal _terminal</code></li></ul><p><strong>Returns</strong></p><ul><li><code>uint256 projectId</code></li></ul><p><a href="write/createfor.md">more</a></p>                                                            |
| **`setHandleOf`**      | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>bytes32 _handle</code></li></ul><p><a href="write/sethandleof.md">more</a></p>                                                                                                                                   |
| **`setUriOf`**         | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>string _uri</code></li></ul><p><a href="write/seturiof.md">more</a></p>                                                                                                                                          |
| **`transferHandleOf`** | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li><li><code>address _transferAddress</code></li><li><code>bytes32 _newHandle</code></li></ul><p><strong>Returns</strong></p><ul><li><code>bytes32 handle</code></li></ul><p><a href="write/transferhandleof.md">more</a></p> |
| **`claimHandle`**      | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li><li><code>address _for</code></li><li><code>uint256 _projectId</code></li></ul><p><a href="write/claimhandle.md">more</a></p>                                                                                                 |
| **`challengeHandle`**  | <p><strong>Params</strong></p><ul><li><code>bytes32 _handle</code></li></ul><p><a href="write/challengehandle.md">more</a></p>                                                                                                                                                                                                                                                     |
| **`renewHandleOf`**    | <p><strong>Traits</strong></p><ul><li><code>requirePermission</code></li></ul><p><strong>Params</strong></p><ul><li><code>uint256 _projectId</code></li></ul><p><a href="write/renewhandleof.md">more</a></p>                                                                                                                                                                      |
