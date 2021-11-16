# recordAddedBalanceFor

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly added funds for the project.**

_Only the associated payment terminal can record an added balance._

# Definition

```solidity
function recordAddedBalanceFor(uint256 _projectId, uint256 _amount)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funds being added belong.
  * `_amount` is the amount added, in wei.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns the current funding cycle for the project.

# Body

1.  Get a reference to the project's current funding cycle that should be returned.

    ```solidity
    // Get a reference to the project's current funding cycle.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
2.  Increment the project's balance by the specified amount.

    ```solidity
    // Increment the balance.
    balanceOf[_projectId] = balanceOf[_projectId] + _amount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly added funds for the project.

  @dev
  Only the associated payment terminal can record an added balance.

  @param _projectId The ID of the project to which the funds being added belong.
  @param _amount The amount added, in wei.

  @return fundingCycle The current funding cycle for the project.
*/
function recordAddedBalanceFor(uint256 _projectId, uint256 _amount)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle)
{
  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Set the balance.
  balanceOf[_projectId] = balanceOf[_projectId] + _amount;
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
