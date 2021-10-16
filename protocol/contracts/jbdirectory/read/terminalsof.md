# terminalsOf

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  For each project ID, the terminals that are currently managing its funds.

  @param _projectId The ID of the project to get terminals of.

  @return An array of terminal addresses.
*/
function terminalsOf(uint256 _projectId) external view override returns (IJBTerminal[] memory) {
  return _terminalsOf[_projectId];
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
