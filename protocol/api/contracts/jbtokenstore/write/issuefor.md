# issueFor

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Issues an project's ERC-20 tokens that'll be used when claiming tokens.**

_Deploys a project's ERC-20 token contract._

_Only a project's current controller can issue its token._

### Definition

```solidity
function issueFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
) external override onlyController(_projectId) returns (IJBToken token) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being issued tokens.
  * `_name` is the ERC-20's name.
  * `_symbol` is the ERC-20's symbol.
* Through the [`onlyController`](../../or-abstract/jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) interface.
* The function returns the token that was issued.

### Body

1.  Make sure a name was provided.

    ```solidity
    // There must be a name.
    if (bytes(_name).length == 0) revert EMPTY_NAME();
    ```
2.  Make sure a symbol was provided.

    ```solidity
    // There must be a symbol.
    if (bytes(_symbol).length == 0) revert EMPTY_SYMBOL();
    ```
3.  Make sure the project doesn't already have a token.

    ```solidity
    // The project shouldn't already have a token.
    if (tokenOf[_projectId] != IJBToken(address(0))) revert PROJECT_ALREADY_HAS_TOKEN();
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
4.  Deploy a new instance of a [`JBToken`](../../jbtoken/) contract. Assign it to the return value.

    ```solidity
    // Deploy the token contract.
    token = new JBToken(_name, _symbol);
    ```
5.  Store the newly deployed token contract as the token of the project.

    ```solidity
    // Store the token contract.
    tokenOf[_projectId] = token;
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
6.  Emit an `Issue` event with the relevant parameters.

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
  Issues an project's ERC-20 tokens that'll be used when claiming tokens.

  @dev
  Deploys a project's ERC-20 token contract.

  @dev
  Only a project's current controller can issue its token.

  @param _projectId The ID of the project being issued tokens.
  @param _name The ERC-20's name.
  @param _symbol The ERC-20's symbol.

  @return token The token that was issued.
*/
function issueFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
) external override onlyController(_projectId) returns (IJBToken token) {
  // There must be a name.
  if (bytes(_name).length == 0) revert EMPTY_NAME();

  // There must be a symbol.
  if (bytes(_symbol).length == 0) revert EMPTY_SYMBOL();

  // The project shouldn't already have a token.
  if (tokenOf[_projectId] != IJBToken(address(0))) revert PROJECT_ALREADY_HAS_TOKEN();

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
| **`EMPTY_NAME`**           | Thrown if a name wasn't specified for the token.   |
| **`EMPTY_SYMBOL`**         | Thrown if a symbol wasn't specified for the token. |
| **`PROJECT_ALREADY_HAS_TOKEN`** | Thrown if the project already has a token.  |
{% endtab %}

{% tab title="Events" %}
| Name                              | Data                                                                                                                                                                                                         |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [**`Issue`**](../events/issue.md)                           | <ul><li><code>uint256 indexed projectId</code></li><li><code>[`IJBToken`](../../../interfaces/ijbtoken.md)indexed token</code></li><li><code>string name</code></li><li><code>string symbol</code></li><li><code>address caller</code></li></ul>                                                                  |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
