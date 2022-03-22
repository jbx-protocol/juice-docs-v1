# remainingDistributionLimitOf

Contract: [`JBETHPaymentTerminal`](../)​‌

Interface: [`IJBPaymentTerminal`](../../../../interfaces/ijbpaymentterminal.md)

{% tabs %}
{% tab title="Step by step" %}
**The amount of a project's funds that can still be distributed within the preconfigured limit.**

### Definition

```solidity
function remainingDistributionLimitOf(
  uint256 _projectId,
  uint256 _fundingCycleConfiguration,
  uint256 _fundingCycleNumber
) external view override returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the remaining limit belongs.
  * `_fundingCycleConfiguration` is the funding cycle configuration during which the limit remaining is being calculated.
  * `_fundingCycleNumber` is the number of the funding cycle during which the limit remaining is being calculated.
* The view function can be accessed externally by anyone.
* The function does not alter state on the blockchain.
* The resulting function overrides a function definition from the `IJBPaymentTerminal` interface.
* The function returns the remaining distribution limit for this terminal.

### Body

1.  Subtract the used distribution limit during the specified funding cycle from the preconfigured distribution limit.

    ```solidity
    // Subtract the used distribution limit during the specified funding cycle from the preconfigured distribution limit.
    return
      directory.controllerOf(_projectId).distributionLimitOf(
        _projectId,
        _fundingCycleConfiguration,
        this
      ) - store.usedDistributionLimitOf(_projectId, _fundingCycleNumber);
    ```

    _External references:_

    * [`distributionLimitOf`](../../../or-controllers/jbcontroller/properties/distributionlimitof.md)
    * [`usedDistributionLimitOf`](../../jbpaymentterminalstore/properties/useddistributionlimitof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  The amount of funds that can still be distributed within the preconfigured limit.

  @param _projectId The ID of the project to which the remaining limit belongs.
  @param _fundingCycleConfiguration The funding cycle configuration during which the limit remaining is being calculated. 
  @param _fundingCycleNumber The number of the funding cycle during which the limit remaining is being calculated. 

  @return The remaining distribution limit for this terminal.
*/
function remainingDistributionLimitOf(
  uint256 _projectId,
  uint256 _fundingCycleConfiguration,
  uint256 _fundingCycleNumber
) external view override returns (uint256) {
  // Subtract the used distribution limit during the specified funding cycle from the preconfigured distribution limit.
  return
    directory.controllerOf(_projectId).distributionLimitOf(
      _projectId,
      _fundingCycleConfiguration,
      this
    ) - store.usedDistributionLimitOf(_projectId, _fundingCycleNumber);
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
