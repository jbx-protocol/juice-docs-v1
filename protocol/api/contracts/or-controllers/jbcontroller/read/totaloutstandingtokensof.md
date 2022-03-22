# totalOutstandingTokensOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Gets the current total amount of outstanding tokens for a project, given a reserved rate.**

### Definition

```solidity
function totalOutstandingTokensOf(uint256 _projectId, uint256 _reservedRate)
  external
  view
  override
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to get total outstanding tokens of.
  * `_reservedRate` is the reserved rate to use when making the calculation.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function returns the current total amount of outstanding tokens for the project.

### Body

1.  Get the total supply of tokens in circulation.

    ```solidity
    // Get the total number of tokens in circulation.
    uint256 _totalSupply = tokenStore.totalSupplyOf(_projectId);
    ```

    _External references:_

    * [`totalSupplyOf`](../../../jbtokenstore/read/totalsupplyof.md)

2.  Get the number of outstanding reserved tokens the project has given the provided reserved rate.

    ```solidity
    // Get the number of reserved tokens the project has.
    uint256 _reservedTokenAmount = _reservedTokenAmountFrom(
      _processedTokenTrackerOf[_projectId],
      _reservedRate,
      _totalSupply
    );
    ```

    _Internal references:_

    * [`_reservedTokenAmountFrom`](\_reservedtokenamountfrom.md)
    * [`_processedTokenTrackerOf`](../properties/\_processedtokentrackerof.md)

3.  Return the sum of the total supply and the reserved tokens.

    ```solidity
    // Add the reserved tokens to the total supply.
    return _totalSupply + _reservedTokenAmount;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Gets the current total amount of outstanding tokens for a project, given a reserved rate.

  @param _projectId The ID of the project to get total outstanding tokens of.
  @param _reservedRate The reserved rate to use when making the calculation.

  @return The current total amount of outstanding tokens for the project.
*/
function totalOutstandingTokensOf(uint256 _projectId, uint256 _reservedRate)
  external
  view
  override
  returns (uint256)
{
  // Get the total number of tokens in circulation.
  uint256 _totalSupply = tokenStore.totalSupplyOf(_projectId);

  // Get the number of reserved tokens the project has.
  uint256 _reservedTokenAmount = _reservedTokenAmountFrom(
    _processedTokenTrackerOf[_projectId],
    _reservedRate,
    _totalSupply
  );

  // Add the reserved tokens to the total supply.
  return _totalSupply + _reservedTokenAmount;
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
