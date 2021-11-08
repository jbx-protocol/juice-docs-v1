# isTerminalDelegateOf

{% tabs %}
{% tab title="Step by step" %}
**Whether or not a specified terminal is a terminal of the specified project.**

# Definition

```solidity
function isTerminalDelegateOf(uint256 _projectId, address _contract)
  public
  view
  override
  returns (bool) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to check within.
  * `_contract` is the address of the terminal to check for.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBDirectory` interface.
* The function returns a flag indicating whether or not the specified terminal is a terminal of the specified project.

# Body

1.  Loop through each of the project's terminals looking for one with the provided contract as its delegate. If it's found, return true.

    ```solidity
    for (uint256 _i; _i < _terminalsOf[_projectId].length; _i++)
      if (address(_terminalsOf[_projectId][_i].delegate()) == _contract) return true;
    ```

    Internal references:

    * [`_terminalsOf`](../properties/_terminalsof.md)
2.  If a terminal with a matching delegate is not found, return false.

    ```solidity
    return false;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Whether or not a specified terminal is a terminal of the specified project.

  @param _projectId The ID of the project to check within.
  @param _contract The address of the terminal to check for.

  @return A flag indicating whether or not the specified terminal is a terminal of the specified project.
*/
function isTerminalDelegateOf(uint256 _projectId, address _contract)
  public
  view
  override
  returns (bool)
{
  for (uint256 _i; _i < _terminalsOf[_projectId].length; _i++)
    if (address(_terminalsOf[_projectId][_i].delegate()) == _contract) return true;
  return false;
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
