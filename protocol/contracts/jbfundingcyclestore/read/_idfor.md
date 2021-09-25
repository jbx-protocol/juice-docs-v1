# \_idFor

{% tabs %}
{% tab title="Step by step" %}

{% endtab %}

{% tab title="Only code" %}
```javascript
/** 
  @notice 
  Constructs a unique ID from a project ID and a number.

  @param _projectId The ID of the project to use in the ID.
  @param _number The number to use in the ID

  @return An ID.
*/
function _idFor(uint256 _projectId, uint256 _number) private pure returns (uint256) {
  return uint256(uint56(_projectId) | uint24(_number));
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

