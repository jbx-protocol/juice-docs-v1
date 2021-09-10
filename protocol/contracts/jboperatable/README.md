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

{% embed url="https://github.com/jbx-protocol/juice-juicehouse/blob/version/2.1/packages/hardhat/contracts/abstract/JBOperatable.sol" %}

## Modifiers

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>requirePermission</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address _account</code> 
          </li>
          <li><code>uint256 _domain</code> 
          </li>
          <li><code>uint256 _index</code>
          </li>
        </ul>
        <p><a href="modifiers/requirepermission.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>requirePermissionAllowingWildcardDomain</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address _account</code>
          </li>
          <li><code>uint256 _domain</code> 
          </li>
          <li><code>uint256 _permissionIndex</code>
          </li>
        </ul>
        <p><a href="modifiers/requirepermissionallowingwildcarddomain.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>requirePermissionAcceptingAlternateAddress</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>address _account</code> 
          </li>
          <li><code>uint256 _domain</code> 
          </li>
          <li><code>uint256 _permissionIndex</code>
          </li>
          <li> <code>address _alternate</code>
          </li>
        </ul>
        <p><a href="modifiers/requirepermissionacceptingalternateaddress.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

## Read

<table>
  <thead>
    <tr>
      <th style="text-align:left">Function</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>operatorStore</code></b>
      </td>
      <td style="text-align:left">
        <p><b>Traits</b>
        </p>
        <ul>
          <li><code>immutable</code>
          </li>
        </ul>
        <p><b>Returns</b>
        </p>
        <ul>
          <li><code>IJBOperationStore operatorStore</code>
          </li>
        </ul>
        <p><a href="read/operatorstore.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

