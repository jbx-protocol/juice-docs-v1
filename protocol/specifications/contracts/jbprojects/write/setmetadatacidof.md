# setMetadataCidOf

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project owner to set the project's IPFS CID hash where metadata about the project has been uploaded.**

_Only a project's owner or operator can set its URI._

## Definition

```solidity
function setMetadataCidOf(uint256 _projectId, string calldata _metadataCid)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.SET_METADATA_CID) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project who's URI is being changed.
  * `_metadataCid` is the new IPFS CID hash where metadata about the project has been uploaded.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.SET_METADATA_CID` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the `IJBProjects` interface.
* The function doesn't return anything.

## Body

1.  Store the new `_metadataCid` as the `metadataCidOf` the project.

    ```solidity
    // Set the new uri.
    metadataCidOf[_projectId] = _metadataCid;
    ```

    _Internal references:_

    * [`metadataCidOf`](../properties/metadatacidof.md)
2.  Emit a `SetMetadataCid` event with the relevant parameters.

    ```solidity
    emit SetMetadataCid(_projectId, _metadataCid, msg.sender);
    ```

    _Event references:_

    * [`SetMetadataCid`](../events/setmetadatacid.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Allows a project owner to set the project's IPFS CID hash where metadata about the project has been uploaded.

  @dev 
  Only a project's owner or operator can set its URI.

  @param _projectId The ID of the project who's URI is being changed.
  @param _metadataCid The new IPFS CID hash where metadata about the project has been uploaded.
*/
function setMetadataCidOf(uint256 _projectId, string calldata _metadataCid)
  external
  override
  requirePermission(ownerOf(_projectId), _projectId, JBOperations.SET_METADATA_CID)
{
  // Set the new uri.
  metadataCidOf[_projectId] = _metadataCid;

  emit SetMetadataCid(_projectId, _metadataCid, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                                                                                         | Data                                                                                                                         |
| ------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| [**`SetMetadataCid`**](../events/setmetadatacid.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>string uri</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
