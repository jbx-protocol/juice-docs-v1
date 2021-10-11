# \_standbyOf

Contract:[`JBFundingCycleStore`](../)​

{% tabs %}
{% tab title="Step by step" %}
**The project's stored funding cycle that hasn't yet started, if one exists.**

_A value of 0 is returned if no funding cycle was found._

Definition:

```solidity
function _standbyOf(uint256 _projectId) private view returns (uint256 fundingCycleId) { ... }
```

* `_projectId` is the ID of a project to look through for a standby cycle.
* The view function is private to this contract.
* The function does not alter state on the blockchain.
* The function returns an ID of a standby funding cycle if one exists, or 0 if one doesn't exist.



1.  Get a reference to the latest funding cycle for the project.\


    _Internal references:_

    * [`latestIdOf`](../properties/latestidof.md)

    ```solidity
    // Get a reference to the project's latest funding cycle.
    fundingCycleId = latestIdOf[_projectId];
    ```


2.  If there isn't a funding cycle for the project, there isn't a standby cycle either.

    ```solidity
    // If there isn't one, theres also no standby funding cycle.
    if (fundingCycleId == 0) return 0;
    ```


3.  Get the struct for the latest funding cycle.\


    _Internal references:_

    * [`_getStructFor`](\_getstructfor.md)

    ```solidity
    // Get the necessary properties for the latest funding cycle.
    JBFundingCycle memory _fundingCycle = _getStructFor(fundingCycleId);
    ```


4.  If the cycle has started, return 0 since there is not a stored funding cycle in standby.

    ```solidity
    // There is no upcoming funding cycle if the latest funding cycle has already started.
    if (block.timestamp >= _fundingCycle.start) return 0;
    ```
{% endtab %}

{% tab title="Only code" %}
```solidity
/**
  @notice 
  The project's stored funding cycle that hasn't yet started, if one exists.
    
  @dev
  A value of 0 is returned if no funding cycle was found.
  
  @param _projectId The ID of a project to look through for a standby cycle.

  @return fundingCycleId The ID of the standby funding cycle.
*/
function _standbyOf(uint256 _projectId) private view returns (uint256 fundingCycleId) {
  // Get a reference to the project's latest funding cycle.
  fundingCycleId = latestIdOf[_projectId];

  // If there isn't one, theres also no standy funding cycle.
  if (fundingCycleId == 0) return 0;

  // Get the necessary properties for the latest funding cycle.
  JBFundingCycle memory _fundingCycle = _getStructFor(fundingCycleId);

  // There is no upcoming funding cycle if the latest funding cycle has already started.
  if (block.timestamp >= _fundingCycle.start) return 0;
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
