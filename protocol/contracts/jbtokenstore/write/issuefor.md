# issueFor

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Issues an owner's ERC-20 Tokens that'll be used when claiming tokens.**

_Deploys an owner's Token ERC-20 token contract._

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
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns the address of the token that was issued.

# Body

1.  Make sure a name was provided.

    ```solidity
    // There must be a name.
    require((bytes(_name).length > 0), '0x1f: EMPTY_NAME');
    ```
2.  Make sure a symbol was provided.

    ```solidity
    // There must be a symbol.
    require((bytes(_symbol).length > 0), '0x20: EMPTY_SYMBOL');
    ```
3.  Make sure the project hasn't already issued a token.

    ```solidity
    // Only one ERC20 token can be issued.
    require(tokenOf[_projectId] == IJBToken(address(0)), '0x21: ALREADY_ISSUED');
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
4.  Deploy a new instance of a [`JBToken`](../../jbtoken.md) contract. Assign it to the return value.

    ```solidity
    // Deploy the token contract.
    token = new JBToken(_name, _symbol);
    ```
5.  Store the newly created token as the `tokenOf` the project.

    ```solidity
    // Store the token contract.
    tokenOf[_projectId] = token;
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
6.  Emit an `Issue` event with the all relevant parameters.

    ```solidity
    emit Issue(_projectId, token, _name, _symbol, msg.sender);
    ```

    _Event references:_

    * [`Issue`](../events/issue.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Issues an owner's ERC-20 Tokens that'll be used when claiming tokens.

  @dev 
  Deploys an owner's Token ERC-20 token contract.
  
  @dev
  Only a project owner or operator can issue its token.

  @param _projectId The ID of the project being issued tokens.
  @param _name The ERC-20's name.
  @param _symbol The ERC-20's symbol.
*/
function issueFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.ISSUE)
  returns (IJBToken token)
{
  // There must be a name.
  require((bytes(_name).length > 0), '0x1f: EMPTY_NAME');

  // There must be a symbol.
  require((bytes(_symbol).length > 0), '0x20: EMPTY_SYMBOL');

  // Only one ERC20 token can be issued.
  require(tokenOf[_projectId] == IJBToken(address(0)), '0x21: ALREADY_ISSUED');

  // Deploy the token contract.
  token = new JBToken(_name, _symbol);

  // Store the token contract.
  tokenOf[_projectId] = token;

  emit Issue(_projectId, token, _name, _symbol, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                     | Description                                        |
| -------------------------- | -------------------------------------------------- |
| **`0x1f: EMPTY_NAME`**     | Thrown if a name wasn't specified for the token.   |
| **`0x20: EMPTY_SYMBOL`**   | Thrown if a symbol wasn't specified for the token. |
| **`0x21: ALREADY_ISSUED`** | Thrown if the project has already issued a token.  |
{% endtab %}

{% tab title="Events" %}
| Name                              | Data                                                                                                                                                                                                         |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`Issue`**](../events/issue.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>IJBToken indexed token</code></li><li><code>string name</code></li><li><code>string symbol</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
