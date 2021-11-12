# issueTokenFor

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Issues an owner's ERC-20 Tokens that'll be used when claiming tokens.**

_Deploys a project's ERC-20 token contract._

_Only a project owner or operator can issue its token._

# Definition

```solidity
function issueFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.ISSUE)
  returns (IJBToken token) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project for which the tokens will be issued.
  * `_name` is the name to associate with the token.
  * `_symbol` is the symbol to associate with the token. This is usually short and all caps.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.ISSUE` permission by the project owner for the provided `_projectId`.
* The function returns the address of the token that was issued.

# Body

1.  Forward the call to the token store.

    ```solidity
    // Issue the token in the store.
    return tokenStore.issueFor(_projectId, _name, _symbol);
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Issues an owner's ERC-20 Tokens that'll be used when claiming tokens.

  @dev 
  Deploys a project's ERC-20 token contract.

  @dev
  Only a project owner or operator can issue its token.

  @param _projectId The ID of the project being issued tokens.
  @param _name The ERC-20's name.
  @param _symbol The ERC-20's symbol.
*/
function issueTokenFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.ISSUE)
  returns (IJBToken token)
{
  // Issue the token in the store.
  return tokenStore.issueFor(_projectId, _name, _symbol);
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
