# setTokenUriResolver

Contract: [`JBProjects`](../)

Interface: [`IJBProjects`](../../../interfaces/ijbprojects.md)

{% tabs %}
{% tab title="Step by step" %}
**Sets the address of the resolver used to retrieve the tokenURI of projects.**

#### Definition

```solidity
function setTokenUriResolver(IJBTokenUriResolver _newResolver) external override onlyOwner { ... }
```

* Arguments:
  * `_newResolver` is the address of the new resolver.
* Through the [`onlyOwner`](https://docs.openzeppelin.com/contracts/4.x/api/access#Ownable-onlyOwner--) modifier, this function can only be accessed by the address that owns this contract.
* The function overrides a function definition from the [`IJBProjects`](../../../interfaces/ijbprojects.md) interface.
* The function doesn't return anything.

#### Body

1.  Store the new resolver.

    ```solidity
    // Store the new resolver.
    tokenUriResolver = _newResolver;
    ```

    _Internal references:_

    * [`tokenUriResolver`](../properties/tokenuriresolver.md)
2.  Emit a `SetTokenUriResolver` event with the relevant parameters.

    ```solidity
    emit SetTokenUriResolver(_newResolver, msg.sender);
    ```

    _Event references:_

    * [`SetTokenUriResolver`](../events/settokenuriresolver.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Sets the address of the resolver used to retrieve the tokenURI of projects.

  @param _newResolver The address of the new resolver.
*/
function setTokenUriResolver(IJBTokenUriResolver _newResolver) external override onlyOwner {
  // Store the new resolver.
  tokenUriResolver = _newResolver;

  emit SetTokenUriResolver(_newResolver, msg.sender);
}
```
{% endtab %}

{% tab title="Events" %}
| Name                                                          | Data                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetTokenUriResolver`**](../events/settokenuriresolver.md) | <ul><li><code>[`IJBTokenUriResolver`](../../../interfaces/ijbtokenuriresolver.md)resolver</code></li><li><code>address caller</code></li></ul>                                                                                                         |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
