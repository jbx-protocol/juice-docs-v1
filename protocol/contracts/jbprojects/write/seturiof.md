# setUriOf

**Contract:**[`JBProjects`](../)

**Interface:** `IJBProjects`

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to set the project's IPFS CID hash where metadata about the project has been uploaded.**

_Only a project's owner or operator can set its URI._  
  
Definition:

```javascript
function setUriOf(uint256 _projectId, string calldata _uri)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, JBOperations.SetUri)
```

* `_projectId` is the ID of the project who's URI is being changed.
* `_uri` is the new IPFS CID hash where metadata about the project has been uploaded. 
* Through the [`requirePermission`](../../jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.SetUri` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBProjects` interface.
* The function doesn't return anything.

1. Store the new `_uri` as the `uriOf` the project.  


   _Internal references:_

   * [`uriOf`](../properties/uriof.md)

   ```javascript
   // Set the new uri.
   uriOf[_projectId] = _uri;
   ```

2. Emit a `SetUri` event with the all relevant parameters.   


   _Event references:_

   * [`SetUri`](../events/seturi.md) 

   ```javascript
   emit SetUri(_projectId, _uri, msg.sender);
   ```
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
  @notice 
  Allows a project owner to set the project's IPFS CID hash where metadata about the project has been uploaded.

  @dev 
  Only a project's owner or operator can set its URI.

  @param _projectId The ID of the project who's URI is being changed.
  @param _uri The new IPFS CID hash where metadata about the project has been uploaded.
  
*/
function setUriOf(uint256 _projectId, string calldata _uri)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, JBOperations.SetUri)
{
    // Set the new uri.
    uriOf[_projectId] = _uri;

    emit SetUri(_projectId, _uri, msg.sender);
}
```
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
      <td style="text-align:left"><b><code>SetUri</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>string uri</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="../events/seturi.md">more</a>
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



