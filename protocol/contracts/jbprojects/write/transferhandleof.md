# transferHandleOf

**Contract:**[`JBProjects`](../)

**Interface:** `IJBProjects`

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to transfer its handle to another address.**

_Only a project's owner or operator can transfer its handle._  
  
Definition:

```javascript
function transferHandleOf(
    uint256 _projectId,
    address _to,
    bytes32 _newHandle
)
    external
    override
    requirePermission(
        ownerOf(_projectId),
        _projectId,
        JBOperations.SetHandle
    )
    returns (bytes32 handle) { ... }
```

* `_projectId` is the ID of the project to transfer the handle from.
* `_to` is the address that should be able to reallocate the transferred handle.
* `_newHandle` is the new unique handle for the project that will replace the transferred one.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.SetHandle` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBProjects` interface.
* The function returns the `handle` that has been transferred.

1. Check that the provided `_newHandle` is not empty.

   ```javascript
   // A new handle must have been provided.
   require(
       _newHandle != bytes32(0),
       "JBProjects::transferHandleOf: EMPTY_HANDLE"
   );
   ```

2. Check that the `_newHandle` is unique. This is done by making sure there isn't yet an `idFor` the handle, and making sure it isn't currently being transferred to an address.  


   _Internal references:_

   * [`idFor`](../properties/idfor.md)
   * [`transferAddressFor`](../properties/transferaddressfor.md)

   ```javascript
    
   // The new handle must be available. 
   require(
       idFor[_newHandle] == 0 && transferAddressFor[_newHandle] == address(0),
       "JBProjects::transferHandleOf: HANDLE_TAKEN"
   );
   ```

3. Get the current `handleOf` the project. Store this is `handle`, which will be returned by the function.  


   _Internal references:_

   * [`handleOf`](../properties/handleof.md)

   ```javascript
   // Get a reference to the project's current handle.
   handle = handleOf[_projectId];
   ```

4. Remove the `idFor` the project's current handle so that the handle no longer resolves to any project ID.  


   _Internal references:_

   * [`idFor`](../properties/idfor.md)

   ```javascript
   // Remove the resolver for the transfered handle.
   idFor[handle] = 0;
   ```

5. Store the provided `_newHandle` as the as the `handleOf` the project.  


   _Internal references:_

   * [`handleOf`](../properties/handleof.md)

   ```javascript
   // Store the new handle for the project ID.
   handleOf[_projectId] = _newHandle;
   ```

6. Store the project's ID as the `idFor` the provided `_newHandle` to allow for project lookup using the handle.  


   _Internal references:_

   * [`idFor`](../properties/idfor.md)

   ```javascript
   // Store the project ID for the new handle.
   idFor[_newHandle] = _projectId;
   ```

7. Store the `_transferAddress` that will be able to transfer the handle as the `transferAddressFor` the handle.  


   _Internal references:_

   * [`transferAddressFor`](../properties/idfor.md)

   ```javascript
   // Give the address the power to transfer the current handle.
   transferAddressFor[handle] = _transferAddress;
   ```

8. Emit a `TransferHandle` event with the all relevant parameters.   


   _Event references:_

   * [`TransferHandle`](../events/seturi.md) 

   ```text
   emit TransferHandle(
       _projectId,
       _transferAddress,
       handle,
       _newHandle,
       msg.sender
   );
   ```
{% endtab %}

{% tab title="Only code" %}
```javascript
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
    requirePermission(
        ownerOf(_projectId),
        _projectId,
        JBOperations.SetHandle
    )
    returns (bytes32 handle)
{
    // A new handle must have been provided.
    require(
        _newHandle != bytes32(0),
        "JBProjects::transferHandleOf: EMPTY_HANDLE"
    );
    
    // The new handle must be available. 
    require(
        idFor[_newHandle] == 0 && transferAddressFor[_newHandle] == address(0),
        "JBProjects::transferHandleOf: HANDLE_TAKEN"
    );

    // Get a reference to the project's current handle.
    handle = handleOf[_projectId];

    // Remove the project ID for the transfered handle.
    idFor[handle] = 0;

    // Store the new handle for the project ID.
    handleOf[_projectId] = _newHandle;
    
    // Store the project ID for the new handle.
    idFor[_newHandle] = _projectId;

    // Give the address the power to transfer the current handle.
    transferAddressFor[handle] = _transferAddress;

    emit TransferHandle(_projectId, _transferAddress, handle, _newHandle, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBProjects::transferHandleOf: EMPTY_HANDLE`** | Thrown if the provided handle is empty |
| **`JBProjects::transferHandleOf: HANDLE_TAKEN`** | Thrown if the provided handle is already in use. |
{% endtab %}

{% tab title="Events" %}
<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>SetHandle</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>address indexed transferAddress</code> 
          </li>
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>bytes32 newHandle</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="../events/transferhandle.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}

