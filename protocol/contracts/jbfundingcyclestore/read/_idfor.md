# \_idFor

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Constructs a unique ID from a project ID and a number.**

### Definition

```solidity
function _idFor(uint256 _projectId, uint256 _number) private pure returns (uint256) { ... }
```

* Arguments:
  * `_projectId`is ID of the project to use in the constructed ID.
  * `_number` is the number to use in the constructed ID.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns the ID that is unique to the provided inputs.

### Body

1.  Return a concatenated `uint256` with the first 56 bits being the `_projectId` and the next 24 bits being the `_number`. Both of these values get packed down into these constraints anyways when they get stored, so theres no additional risk of data loss by doing this.

    ```solidity
    return uint256(uint56(_projectId) | uint24(_number));
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Constructs a unique ID from a project ID and a number.

  @param _projectId The ID of the project to use in the ID.
  @param _number The number to use in the ID

  @return The ID that is unique to the provided inputs.
*/
function _idFor(uint256 _projectId, uint256 _number) private pure returns (uint256) {
  return uint256(uint56(_projectId) | uint24(_number));
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
