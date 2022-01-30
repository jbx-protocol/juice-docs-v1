# createFor

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Create a new project for the specified owner, which mints an NFT (ERC-721) into their wallet.**

_Anyone can create a project on an owner's behalf._

### Definition

```solidity
function createFor(address _owner, JBProjectMetadata calldata _metadata)
  external
  override
  returns (uint256) { ... }
```

* Arguments:
  * `_owner` is the address that will be the owner of the project.
  * `_metadata` is a struct containing metadata content about the project, and domain within which the metadata applies. An empty string is acceptable if no metadata is being provided.
* The function can be accessed externally by anyone.
* The function overrides a function definition from the `IJBProjects` interface.
* Returns the token ID of the newly created project.

### Body

1.  Increment the count to include the new project being created.

    ```solidity
    // Increment the count, which will be used as the ID.
    count++;
    ```

    _Internal references:_

    * [`count`](../properties/count.md)
2.  Mint a new NFT token belonging to the `_owner` using the `count` as the token ID.

    ```solidity
    // Mint the project.
    _safeMint(_owner, count);
    ```

    _Internal references:_

    * [`_safeMint`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-\_safeMint-address-uint256-bytes-)
3.  If metadata was provided (meaning it's content is not an empty string), store it as the `content` for newly created project under the provided `domain`.

    ```solidity
    // Set the metadata if one was provided.
    if (bytes(_metadata.content).length > 0)
      metadataContentOf[count][_metadata.domain] = _metadata.content;
    ```

    _Internal references:_

    * [`metadataContentOf`](../properties/metadatacontentof.md)
4.  Emit a `Create` event with all relevant parameters.

    ```
    emit Create(count, _owner, _metadata, msg.sender);
    ```

    _Event references:_

    * [`Create`](../events/create.md)
5.  Return the newly created project's token ID.

    ```solidity
    return count;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Create a new project for the specified owner, which mints an NFT (ERC-721) into their wallet.

  @dev 
  Anyone can create a project on an owner's behalf.

  @param _owner The address that will be the owner of the project.
  @param _metadata A struct containing metadata content about the project, and domain within which the metadata applies. An empty string is acceptable if no metadata is being provided.

  @return The token ID of the newly created project
*/
function createFor(address _owner, JBProjectMetadata calldata _metadata)
  external
  override
  returns (uint256)
{
  // Increment the count, which will be used as the ID.
  count++;

  // Mint the project.
  _safeMint(_owner, count);

  // Set the metadata if one was provided.
  if (bytes(_metadata.content).length > 0)
    metadataContentOf[count][_metadata.domain] = _metadata.content;

  emit Create(count, _owner, _metadata, msg.sender);

  return count;
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                | Data                                                                                                                                                                                                                                                  |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Create`**](../events/create.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>address indexed owner</code></li><li><a href="../../data-structures/jbprojectmetadata/"><code>JBProjectMetadata</code></a><code>uri</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
