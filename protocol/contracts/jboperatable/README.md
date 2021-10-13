---
description: >-
  Modifiers to allow access to functions based on the message sender's operator
  status.
---

# JBOperatable

## Overview

### Traits

`abstract`

### Code

{% embed url="https://github.com/jbx-protocol/juice-contracts/tree/main/contracts/v2abstract/JBOperatable.sol" %}

## Modifiers

| Name                                             | Data                                                                                                                                                                                                                                                        |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`requirePermission`**                          | <ul><li><code>address _account</code></li><li><code>uint256 _domain</code></li><li><code>uint256 _index</code></li></ul><p><a href="modifiers/requirepermission.md">more</a></p>                                                                            |
| **`requirePermissionAllowingWildcardDomain`**    | <ul><li><code>address _account</code></li><li><code>uint256 _domain</code></li><li><code>uint256 _permissionIndex</code></li></ul><p><a href="modifiers/requirepermissionallowingwildcarddomain.md">more</a></p>                                            |
| **`requirePermissionAcceptingAlternateAddress`** | <ul><li><code>address _account</code></li><li><code>uint256 _domain</code></li><li><code>uint256 _permissionIndex</code></li><li><code>address _alternate</code></li></ul><p><a href="modifiers/requirepermissionacceptingalternateaddress.md">more</a></p> |

## Read

| Function            | Definition                                                                                                                                                                                                                                                                        |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`operatorStore`** | <p><strong>Traits</strong></p><ul><li><code>immutable</code></li></ul><p><strong>Returns</strong></p><ul><li><a href="../../interfaces/ijboperatorstore.md"><code>IJBOperationStore</code></a><code>operatorStore</code></li></ul><p><a href="read/operatorstore.md">more</a></p> |
