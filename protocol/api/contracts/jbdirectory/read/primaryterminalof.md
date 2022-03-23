# primaryTerminalOf

{% tabs %}
{% tab title="Step by step" %}
**The primary terminal that is managing funds for a project for a specified token.**

_Contracts should send tokens of the specified type to a project's primary terminal._

_The zero address is returned if a terminal isn't found for the specified token._

#### Definition

```solidity
function primaryTerminalOf(uint256 _projectId, address _token)
  public
  view
  override
  returns (IJBPaymentTerminal) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get a terminal for.
  * `_token` is the token the terminal accepts.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBDirectory`](../../../interfaces/ijbdirectory.md) interface.
* The function returns the primary terminal for the project for the specified token.

#### Body

1.  Check to see if the project has explicitly set a primary terminal for this token. If so, return it.

    ```solidity
    // If a primary terminal for the token was specifically set, return it.
    if (_primaryTerminalOf[_projectId][_token] != IJBPaymentTerminal(address(0)))
      return _primaryTerminalOf[_projectId][_token];
    ```

    Internal references:

    * [`_primaryTerminalOf`](../properties/_primaryterminalof.md)
2.  Loop through each of the project's terminals looking for one that uses the same token as the one specified. If one is found, return it.

    ```solidity
    // Return the first terminal which accepts the specified token.
    for (uint256 _i; _i < _terminalsOf[_projectId].length; _i++) {
      IJBPaymentTerminal _terminal = _terminalsOf[_projectId][_i];
      if (_terminal.token() == _token) return _terminal;
    }
    ```

    Internal references:

    * [`_terminalsOf`](../properties/_terminalsof.md)
3.  Return an empty terminal if not found.

    ```solidity
    // Not found.
    return IJBPaymentTerminal(address(0));
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  The primary terminal that is managing funds for a project for a specified token.

  @dev
  Contracts should send tokens of the specified type to a project's primary terminal.

  @dev
  The zero address is returned if a terminal isn't found for the specified token.

  @param _projectId The ID of the project to get a terminal for.
  @param _token The token the terminal accepts.

  @return The primary terminal for the project for the specified token.
*/
function primaryTerminalOf(uint256 _projectId, address _token)
  public
  view
  override
  returns (IJBPaymentTerminal)
{
  // If a primary terminal for the token was specifically set, return it.
  if (_primaryTerminalOf[_projectId][_token] != IJBPaymentTerminal(address(0)))
    return _primaryTerminalOf[_projectId][_token];

  // Return the first terminal which accepts the specified token.
  for (uint256 _i; _i < _terminalsOf[_projectId].length; _i++) {
    IJBPaymentTerminal _terminal = _terminalsOf[_projectId][_i];
    if (_terminal.token() == _token) return _terminal;
  }

  // Not found.
  return IJBPaymentTerminal(address(0));
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
