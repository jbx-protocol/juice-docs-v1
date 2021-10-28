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
  onlyOwner
  returns (JBFundingCycle memory fundingCycle) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to which the funds being added belong.
  * `_amount` is the amount added, in wei.
* Through the [`requirePermission`](../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, from an operator that has been given the `JBOperations.BURN` permission by the project owner for the provided `_projectId`, or from the one of the project's terminal's delegates..
* The function overrides a function definition from the [`IJBController`](../../../../interfaces/ijbcontroller.md) interface.
* The function doesn't return anything.

# Body

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

{% tab title="Errors" %}
| String             | Description                                                                                                                |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| **`0x32: NO_OP`**  | Thrown if no tokens are being burned.                                                                                      |
| **`0x33: PAUSED`** | Thrown if the request is not being made by a payment terminal, and the project's current funding cycle has paused burning. |
{% endtab %}

{% tab title="Events" %}
| Name                                        | Data                                                                                                                                                                                                                                                       |
| ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`BurnTokens`**](../events/burntokens.md) | <p>ul></p><ul><li><code>address indexed holder</code></li></ul><ul><li><code>uint256 indexed projectId</code></li></ul><ul><li><code>uint256 count</code></li></ul><ul><li><code>string memo</code></li></ul><ul><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
