# _isApproved

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**Checks to see if the provided funding cycle is approved according to the correct ballot.**

### Definition

```solidity
function _isApproved(uint256 _projectId, JBFundingCycle memory _fundingCycle)
  private
  view
  returns (bool) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funding cycle belongs. 
  * `_fundingCycle` is the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) to get an approval flag for.
* The view function is private to this contract.
* The view function does not alter state on the blockchain.
* The function returns the approval flag.

### Body

1.  Check to see if the state of the ballot for the provided funding cycle configuration is approved. The ballot that should be used is that of the funding cycle that the provided one is based on. This is because each funding cycle's ballot dictates the approval conditions of the next proposed reconfiguration.

    ```solidity
    return
      _ballotStateOf(_projectId, _fundingCycle.configuration, _fundingCycle.basedOn) ==
      JBBallotState.Approved;
    ```

    _Internal references:_

    * [`_ballotStateOf`](_ballotstateof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Checks to see if the provided funding cycle is approved according to the correct ballot.

  @param _projectId The ID of the project to which the funding cycle belongs. 
  @param _fundingCycle The funding cycle to get an approval flag for.

  @return The approval flag.
*/
  function _isApproved(uint256 _projectId, JBFundingCycle memory _fundingCycle)
    private
    view
    returns (bool)
  {
    return
      _ballotStateOf(_projectId, _fundingCycle.configuration, _fundingCycle.basedOn) ==
      JBBallotState.Approved;
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
