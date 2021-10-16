# primaryTerminalOf

{% tabs %}
{% tab title="Step by step" %}
**The primary terminal that is managing funds for a project for a specified token.**

_Only a project's current controller can burn its tokens._

Definition:

```solidity
function burnFrom(
  address _holder,
  uint256 _projectId,
  uint256 _amount,
  bool _preferClaimedTokens
) external override onlyController(_projectId) { ... }
```

* Arguments:
  * `_holder` is the address address that owns the tokens being burned.
  * `_projectId` is the ID of the project to which the burned tokens belong.
  * `_amount` is the amount of tokens to burn.
  * `_preferClaimedTokens` is a flag indicating if there's a preference to burn tokens that have been converted to ERC-20s.
* Through the [`onlyController`](../../jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBTokenStore` interface.
* The function returns nothing.
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  The primary terminal that is managing funds for a project for a specified token.

  @param _projectId The ID of the project to get a terminal for.
  @param _token The token the terminal accepts.

  @return The primary terminal for the project for the specified token.
*/
function primaryTerminalOf(uint256 _projectId, address _token)
  public
  view
  override
  returns (IJBTerminal)
{
  // If a primary terminal for the token was specifically set, return it.
  if (_primaryTerminalOf[_projectId][_token] != IJBTerminal(address(0)))
    return _primaryTerminalOf[_projectId][_token];

  // return the first terminal which accepts the specified token.
  for (uint256 _i; _i < _terminalsOf[_projectId].length; _i++) {
    IJBTerminal _terminal = _terminalsOf[_projectId][_i];
    if (_terminal.vault().token() == _token) return _terminal;
  }

  // Not found.
  return IJBTerminal(address(0));
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category | Description | Reward |
| :--- | :--- | :--- |
| **Optimization** | Help make this operation more efficient. | 0.5ETH |
| **Low severity** | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds. | 5+ETH |
{% endtab %}
{% endtabs %}
