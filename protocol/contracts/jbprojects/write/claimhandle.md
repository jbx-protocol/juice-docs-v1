# claimHandle

Contract:[`JBProjects`](../)

Interface:`IJBProjects`

{% tabs %}
{% tab title="Step by step" %}
**Allows an address to claim an handle that has been transferred to it, and apply it to a project of theirs.**

**A handle can also be claimed if it has been challenged and the challenge has succeeded.**

_Only a project's owner or operator can claim a handle for it._\
\
Definition:

```solidity
function claimHandle(
  bytes32 _handle,
  address _transferAddress,
  uint256 _projectId
)
  external
  override
  requirePermissionAllowingWildcardDomain(_transferAddress, _projectId, JBOperations.CLAIM_HANDLE)
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.CLAIM_HANDLE) { ... }
```

* `_handle` is the handle being claimed.
* `_transferAddress` is the address to which the handle has been transferred, which can now assign the handle to a project.
* `_projectId` is the ID of the project to assign to the claimed handle.
* Through the [`requirePermissionAllowingWildcardDomain`](../../jboperatable/modifiers/requirepermissionallowingwildcarddomain.md) modifier, the function is only accessible by the `_transferAddress`, from an operator that has been given the `JBOperations.CLAIM_HANDLE` permission by the `_transferAddress` for the provided `_projectId`, or from an operator that has been given the `JBOperations.CLAIM_HANDLE` permission by the `_transferAddress`for no specific domain.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.CLAIM_HANDLE` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBProjects` interface.
* The function doesn't return anything.



1.  Check that either the `transferAddressFor` the `_handle` is the provided `_transferAddress`, or that the `_handle` is being challenged and the `challengeExpiryOf` the `_handle` has successfully passed.



    _Internal references:_

    * [`transferAddressFor`](../properties/transferaddressfor.md)
    * [`challengeExpiryOf`](../properties/challengeexpiryof.md)

    ```solidity
    // The handle must have been transferred to the specified address,
    // or the handle challenge must have expired before being renewed.
    require(
      transferAddressFor[_handle] == _transferAddress ||
        (challengeExpiryOf[_handle] > 0 && block.timestamp > challengeExpiryOf[_handle]),
      '0x0c: UNAUTHORIZED'
    );
    ```


2.  Remove the `idFor` the `handleOf` the project so that the handle no longer resolves to any project ID.



    _Internal references:_

    * [`handleOf`](../properties/handleof.md)
    * [`idFor`](../properties/idfor.md)

    ```solidity
    // Remove the project ID for the current handle of the specified project.
    idFor[handleOf[_projectId]] = 0;
    ```


3.  Store the project's ID as the `idFor` the provided `_handle` to allow for project lookup using the handle.



    _Internal references:_

    * [`idFor`](../properties/idfor.md)

    ```solidity
    // Set the project ID for the provided handle to be the specified project.
    idFor[_handle] = _projectId;
    ```


4.  Store the provided `_handle` as the as the `handleOf` the project.



    _Internal references:_

    * [`handleOf`](../properties/handleof.md)

    ```solidity
    // Set the new handle.
    handleOf[_projectId] = _handle;
    ```


5.  Remove the `transferAddressFor` the handle since it has now been claimed and is no longer being transferred.



    _Internal references:_

    * [`transferAddressFor`](../properties/transferaddressfor.md)

    ```solidity
    // Set the handle as not being transferred.
    transferAddressFor[_handle] = address(0);
    ```


6.  Remove the `challengeExpiryOf` the handle since it has now been transferred to a new project and must have a fresh challenge period awaited before being claimed again.



    _Internal references:_

    * [`challengeExpiryOf`](../properties/challengeexpiryof.md)

    ```solidity
    // Reset the challenge to 0.
    challengeExpiryOf[_handle] = 0;
    ```


7.  Emit a `TransferHandle` event with the all relevant parameters.



    _Event references:_

    * [`ClaimHandle`](../events/claimhandle.md)

    ```solidity
    emit ClaimHandle(_projectId, _transferAddress, _handle, msg.sender);
    ```
{% endtab %}

{% tab title="Only code" %}
```solidity
/**
  @notice 
  Allows an address to claim an handle that has been transferred to it, and apply it to a project of theirs.
  A handle can also be claimed if it has been challenged and the challenge has succeeded.

  @dev 
  Only a project's owner or operator can claim a handle for it.

  @param _handle The handle being claimed.
  @param _transferAddress The address to which the handle has been transferred, which can now assign the handle to a project.
  @param _projectId The ID of the project to assign to the claimed handle.
*/
function claimHandle(
  bytes32 _handle,
  address _transferAddress,
  uint256 _projectId
)
  external
  override
  requirePermissionAllowingWildcardDomain(_transferAddress, _projectId, JBOperations.CLAIM_HANDLE)
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.CLAIM_HANDLE)
{
  // The handle must have been transferred to the specified address,
  // or the handle challenge must have expired before being renewed.
  require(
    transferAddressFor[_handle] == _transferAddress ||
      (challengeExpiryOf[_handle] > 0 && block.timestamp > challengeExpiryOf[_handle]),
    '0x0c: UNAUTHORIZED'
  );

  // Remove the project ID for the current handle of the specified project.
  idFor[handleOf[_projectId]] = 0;

  // Set the project ID for the provided handle to be the specified project.
  idFor[_handle] = _projectId;

  // Set the new handle.
  handleOf[_projectId] = _handle;

  // Set the handle as not being transferred.
  transferAddressFor[_handle] = address(0);

  // Reset the challenge to 0.
  challengeExpiryOf[_handle] = 0;

  emit ClaimHandle(_projectId, _transferAddress, _handle, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                                                                       |
| ------------------------ | ------------------------------------------------------------------------------------------------- |
| **`0x0c: UNAUTHORIZED`** | Thrown if the message sender does not have access to the function using the specified parameters. |
{% endtab %}

{% tab title="Events" %}
| Name              | Data                                                                                                                                                                                                                                            |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`ClaimHandle`** | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed transferAddress</code></li><li><code>bytes32 indexed handle</code></li><li><code>address caller</code></li></ul><p><a href="../events/claimhandle.md">more</a></p> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
