# setHandleOf

**Contract:**[`JBProjects`](../)

**Interface:** `IJBProjects`

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to set the project's handle.**

_Only a project's owner or operator can set its handle._  
  
Definition:

```javascript
function setHandleOf(uint256 _projectId, bytes32 _handle)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, JBOperations.SetHandle)
```

* `_projectId` is the ID of the project who's handle is being changed.
* `_handle` is the new unique handle for the project.
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.SetHandle` permission.
* The function overrides a function definition from the `IJBProjects` interface.
* The function doesn't return anything.

1. Check that the provided `_handle` is not empty.

   ```javascript
   // Handle must exist.
   require(_handle != bytes32(0), "JBProjects::setHandleOf: EMPTY_HANDLE");
   ```

2. 
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Allows a project owner to set the project's handle.

  @dev 
  Only a project's owner or operator can set its handle.

  @param _projectId The ID of the project who's handle is being changed.
  @param _handle The new unique handle for the project.
  
*/
function setHandleOf(uint256 _projectId, bytes32 _handle)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, JBOperations.SetHandle)
{
    // Handle must exist.
    require(_handle != bytes32(0), "JBProjects::setHandleOf: EMPTY_HANDLE");

    // Handle must be unique.
    require(
        idFor[_handle] == 0 && transferAddressFor[_handle] == address(0),
        "JBProjects::setHandleOf: HANDLE_TAKEN"
    );

    // Register the change in the resolver.
    idFor[handleOf[_projectId]] = 0;

    idFor[_handle] = _projectId;
    handleOf[_projectId] = _handle;

    emit SetHandle(_projectId, _handle, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBProjects::setHandleOf: EMPTY_HANDLE`** | Thrown if the provided handle is empty |
| **`JBProjects::setHandleOf: HANDLE_TAKEN`** | Thrown if the provided handle is already in use. |
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
          <li><code>bytes32 indexed handle</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="../events/sethandle.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.25ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 0.75ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 3+ETH |
{% endtab %}
{% endtabs %}



