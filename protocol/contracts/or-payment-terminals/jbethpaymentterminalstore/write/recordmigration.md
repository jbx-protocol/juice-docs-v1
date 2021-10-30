# recordMigration

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records the migration of this terminal to another.**

_Only the associated payment terminal can record an added balance._

# Definition

```solidity
function recordAddedBalanceFor(uint256 _projectId, uint256 _amount)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project being migrated.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns the project's current balance.

# Body

1.  Get a reference to the project's current funding cycle that should be returned.

    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
2.  Make sure that migrating terminals is allowed by the current funding cycle.

    ```solidity
    // Migration must be allowed
    require(_fundingCycle.terminalMigrationAllowed(), '0x4a: NOT_ALLOWED');
    ```
3.  Get a reference to the project's current balance. Set this to the value that the function will return.

    ```solidity
    // Return the current balance.
    balance = balanceOf[_projectId];
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
4.  Set the project's balance to 0 since funds are moving away from this terminal.

    ```solidity
    // Set the balance to 0.
    balanceOf[_projectId] = 0;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice
  Records the migration of this terminal to another.

  @param _projectId The ID of the project being migrated.

  @return balance The project's current balance.
*/
function recordMigration(uint256 _projectId)
  external
  onlyAssociatedPaymentTerminal
  returns (uint256 balance)
{
  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Migration must be allowed
  require(_fundingCycle.terminalMigrationAllowed(), '0x4a: NOT_ALLOWED');

  // Return the current balance.
  balance = balanceOf[_projectId];

  // Set the balance to 0.
  balanceOf[_projectId] = 0;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                  | Description                                                                  |
| ----------------------- | ---------------------------------------------------------------------------- |
| **`0x4a: NOT_ALLOWED`** | Thrown if the project's current funding cycle disallows terminal migrations. |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
