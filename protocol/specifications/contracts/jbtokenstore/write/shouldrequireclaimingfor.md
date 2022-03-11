# shouldRequireClaimingFor

Contract: [`JBTokenStore`](../)​‌

Interface: [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md)

{% tabs %}
{% tab title="Step by step" %}
**Allows a project to force all future mints of its tokens to be claimed into the holder's wallet, or revoke the flag if it's already set.**

_Only a token holder or an operator can require claimed token._

### Definition

```solidity
function shouldRequireClaimingFor(uint256 _projectId, bool _flag)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REQUIRE_CLAIM) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being affected.
  * `_flag` is a flag indicating whether or not claiming should be required.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.REQUIRE_CLAIM` permission by the project owner for the provided `_projectId`.
* The function overrides a function definition from the [`IJBTokenStore`](../../../interfaces/ijbtokenstore.md) interface.
* The function doesn't return anything.

### Body

1.  Get a reference to the project's current token.

    ```solidity
    // Get a reference to the project's current token.
    IJBToken _token = tokenOf[_projectId];
    ```

    _Internal references:_

    * [`tokenOf`](../properties/tokenof.md)
2.  Make sure the project has a token. If it doesn't, there's nowhere to claim tokens onto.

    ```solidity
    // The project must have a token contract attached.
    if (_token == IJBToken(address(0))) revert TOKEN_NOT_FOUND();
    ```
3.  Store the flag for the project.

    ```solidity
    // Store the flag.
    requireClaimFor[_projectId] = _flag;
    ```

    _Internal references:_

    * [`requireClaimFor`](../properties/requireclaimfor.md)
4.  Emit a `ShouldRequireClaim` event with the relevant parameters.

    ```solidity
    emit ShouldRequireClaim(_projectId, _flag, msg.sender);
    ```

    _Event references:_

    * [`ShouldRequireClaim`](../events/shouldrequireclaim.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Allows a project to force all future mints of its tokens to be claimed into the holder's wallet, or revoke the flag if it's already set.

  @dev
  Only a token holder or an operator can require claimed token.

  @param _projectId The ID of the project being affected.
  @param _flag A flag indicating whether or not claiming should be required.
*/
function shouldRequireClaimingFor(uint256 _projectId, bool _flag)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.REQUIRE_CLAIM)
{
  // Get a reference to the project's current token.
  IJBToken _token = tokenOf[_projectId];

  // The project must have a token contract attached.
  if (_token == IJBToken(address(0))) revert TOKEN_NOT_FOUND();

  // Store the flag.
  requireClaimFor[_projectId] = _flag;

  emit ShouldRequireClaim(_projectId, _flag, msg.sender);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                | Description                                      |
| --------------------- | ------------------------------------------------ |
| **`TOKEN_NOT_FOUND`** | Thrown if the project doesn't have a token contract attached. |
{% endtab %}

{% tab title="Events" %}
| Name                                                        | Data                                                                                                                                |
| ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| [**`ShouldRequireClaim`**](events/shouldrequireclaim.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>bool indexed flag</code></li><li><code>address caller</code></li></ul>                                                                                                                                           |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
