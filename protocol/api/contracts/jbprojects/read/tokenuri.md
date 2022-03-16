# tokenURI

Contract: [`JBProjects`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Returns the URI where the ERC-721 standard JSON of a project is hosted.**

### Definition

```solidity
function tokenURI(uint256 _projectId) public view override returns (string memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get a URI of.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`ERC721Votes`](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721Votes) contract.
* The function returns the token URI to use for the provided `_projectId`.

### Body

1.  Return an empty string if there is no URI resolver set.

    ```solidity
    // If there's no resolver, there's no URI.
    if (tokenUriResolver == IJBTokenUriResolver(address(0))) return '';
    ```

    _Internal references:_

    * [`tokenUriResolver`](../properties/tokenuriresolver.md)
2.  Resolve the URI for the project.

    ```solidity
    // Return the resolved URI.
    return tokenUriResolver.getUri(_projectId);
    ```

    _External references:_

    * [`getUri`](../../../interfaces/ijbtokenuriresolver.md)

{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Returns the URI where the ERC-721 standard JSON of a project is hosted.

  @param _projectId The ID of the project to get a URI of.

  @return The token URI to use for the provided `_projectId`.
*/
function tokenURI(uint256 _projectId) public view override returns (string memory) {
  // If there's no resolver, there's no URI.
  if (tokenUriResolver == IJBTokenUriResolver(address(0))) return '';

  // Return the resolved URI.
  return tokenUriResolver.getUri(_projectId);
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
