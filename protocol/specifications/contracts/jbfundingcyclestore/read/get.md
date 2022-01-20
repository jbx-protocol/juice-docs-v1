# get

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Get the funding cycle with the given configuration for the specified project.**

## Definition

```solidity
function get(uint256 _projectId, uint256 _configuration)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funding cycle belongs.
  * `_configuration` is the configuration of the funding cycle to get.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* The function returns a [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md).

## Body

1.  Return the struct for the provided configuration and project.

    ```solidity
    return _getStructFor(_projectId, _configuration);
    ```

    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice 
  Get the funding cycle with the given configuration for the specified project.

  @param _projectId The ID of the project to which the funding cycle belongs.
  @param _configuration The configuration of the funding cycle to get.

  @return fundingCycle The funding cycle.
*/
function get(uint256 _projectId, uint256 _configuration)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle)
{
  return _getStructFor(_projectId, _configuration);
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
