# setMetadataOf

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to set the project's metadata content for a particular domain namespace.**

_Only a project's owner or operator can set its metadata._

_Applications can use the domain namespace as they wish._

#### Definition

```solidity
function setMetadataOf(uint256 _projectId, JBProjectMetadata calldata _metadata)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.SET_METADATA) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project who's metadata is being changed.
  * `_metadata` is the struct containing metadata content, and domain within which the metadata applies.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the [`JBOperations.SET_METADATA`](../../../libraries/jboperations.md) permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the [`IJBProjects`](../../../interfaces/ijbprojects.md) interface.
* The function doesn't return anything.

#### Body

1.  Store the project's new metadata content within the specified domain.

    ```solidity
    // Set the project's new metadata content within the specified domain.
    metadataContentOf[_projectId][_metadata.domain] = _metadata.content;
    ```

    _Internal references:_

    * [`metadataContentOf`](../properties/metadatacontentof.md)
2.  Emit a `SetMetadataCid` event with the relevant parameters.

    ```solidity
    emit SetMetadata(_projectId, _metadata, msg.sender);
    ```

    _Event references:_

    * [`SetMetadata`](../events/setmetadata.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Allows a project owner to set the project's metadata content for a particular domain namespace. 

  @dev 
  Only a project's owner or operator can set its metadata.

  @dev 
  Applications can use the domain namespace as they wish.

  @param _projectId The ID of the project who's metadata is being changed.
  @param _metadata A struct containing metadata content, and domain within which the metadata applies. 
*/
function setMetadataOf(uint256 _projectId, JBProjectMetadata calldata _metadata)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.SET_METADATA)
{
  // Set the project's new metadata content within the specified domain.
  metadataContentOf[_projectId][_metadata.domain] = _metadata.content;

  emit SetMetadata(_projectId, _metadata, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                             | Data                                                                                                                                        |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetMetadata`**](../events/setmetadata.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`JBProjectMetadata`](../../data-structures/jbprojectmetadata.md)metadata</code></li><li><code>address caller</code></li></ul>                                                                                                         |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
