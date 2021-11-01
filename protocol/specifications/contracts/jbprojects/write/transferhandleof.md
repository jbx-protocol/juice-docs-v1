# transferHandleOf

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to transfer its handle to another address.**

_Only a project's owner or operator can transfer its handle._

# Definition

```solidity
function transferHandleOf(
  uint256 _projectId,
  address _transferAddress,
  bytes32 _newHandle
)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.SET_HANDLE)
  returns (bytes32 handle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to transfer the handle from.
  * `_to` is the address that should be able to reallocate the transferred handle.
  * `_newHandle` is the new unique handle for the project that will replace the transferred one.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.SET_HANDLE` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBProjects` interface.
* The function returns the `handle` that has been transferred.

# Body

1.  Check that the provided `_newHandle` is not empty.

    ```solidity
    // A new handle must have been provided.
    require(_newHandle != bytes32(0), '0x0a: EMPTY_HANDLE');
    ```
2.  Check that the `_newHandle` is unique. This is done by making sure there isn't yet an `idFor` the handle, and making sure it isn't currently being transferred to an address.

    ```solidity
    // The new handle must be available.
    require(idFor[_newHandle] == 0 && transferAddressFor[_newHandle] == address(0), '0x0b: HANDLE_TAKEN');
    ```

    _Internal references:_

    * [`idFor`](../properties/idfor.md)
    * [`transferAddressFor`](../properties/transferaddressfor.md)
3.  Get the current `handleOf` the project. Store this is `handle`, which will be returned by the function.

    ```solidity
    // Get a reference to the project's current handle.
    handle = handleOf[_projectId];
    ```

    _Internal references:_

    * [`handleOf`](../properties/handleof.md)
4.  Remove the `idFor` the project's current handle so that the handle no longer resolves to any project ID.

    ```solidity
    // Remove the resolver for the transferred handle.
    idFor[handle] = 0;
    ```

    _Internal references:_

    * [`idFor`](../properties/idfor.md)
5.  Store the project's ID as the `idFor` the provided `_newHandle` to allow for project lookup using the handle.

    ```solidity
    // Store the new handle for the project ID.
    idFor[_newHandle] = _projectId;
    ```

    _Internal references:_

    * [`idFor`](../properties/idfor.md)
6.  Store the provided `_newHandle` as the as the `handleOf` the project.

    ```solidity
    // Store the new handle for the project ID.
    handleOf[_projectId] = _newHandle;
    ```

    _Internal references:_

    * [`handleOf`](../properties/handleof.md)
7.  Store the `_transferAddress` that will be able to transfer the handle as the `transferAddressFor` the handle.

    ```solidity
    // Give the address the power to transfer the current handle.
    transferAddressFor[handle] = _transferAddress;
    ```

    _Internal references:_

    * [`transferAddressFor`](../properties/transferaddressfor.md)
8.  Emit a `TransferHandle` event with the relevant parameters.

    ```
    emit TransferHandle(_projectId, _transferAddress, handle, _newHandle, msg.sender);
    ```

    _Event references:_

    * [`TransferHandle`](../events/transferhandle.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Allows a project owner to transfer its handle to another address.

  @dev 
  Only a project's owner or operator can transfer its handle.

  @param _projectId The ID of the project to transfer the handle from.
  @param _transferAddress The address that should be able to reallocate the transferred handle.
  @param _newHandle The new unique handle for the project that will replace the transferred one.

  @return handle The handle that has been transferred.
*/
function transferHandleOf(
  uint256 _projectId,
  address _transferAddress,
  bytes32 _newHandle
)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.SET_HANDLE)
  returns (bytes32 handle)
{
  // A new handle must have been provided.
  require(_newHandle != bytes32(0), '0x0a: EMPTY_HANDLE');

  // The new handle must be available.
  require(idFor[_newHandle] == 0 && transferAddressFor[_newHandle] == address(0), '0x0b: HANDLE_TAKEN');

  // Get a reference to the project's current handle.
  handle = handleOf[_projectId];

  // Remove the project ID for the transferred handle.
  idFor[handle] = 0;

  // Store the new handle for the project ID.
  idFor[_newHandle] = _projectId;

  // Store the project ID for the new handle.
  handleOf[_projectId] = _newHandle;

  // Give the address the power to transfer the current handle.
  transferAddressFor[handle] = _transferAddress;

  emit TransferHandle(_projectId, _transferAddress, handle, _newHandle, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                   | Description                                      |
| ------------------------ | ------------------------------------------------ |
| **`0x0a: EMPTY_HANDLE`** | Thrown if the provided handle is empty           |
| **`0x0b: HANDLE_TAKEN`** | Thrown if the provided handle is already in use. |
{% endtab %}

{% tab title="Events" %}
| Name                                      | Data                                                                                                                                                                                                                                 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`SetHandle`**](../events/sethandle.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed transferAddress</code></li><li><code>bytes32 indexed handle</code></li><li><code>bytes32 newHandle</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
