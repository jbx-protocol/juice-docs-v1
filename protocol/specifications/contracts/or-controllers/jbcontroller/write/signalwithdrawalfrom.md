# signalWithdrawalFrom

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../../../protocol/interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Signals that a project's funds are being withdrawn.**

_Only a project's terminal can signal a withdrawal._

## Definition

```solidity
function signalWithdrawlFrom(uint256 _projectId, uint256 _amount)
  external
  override
  onlyTerminal(_projectId)
  returns (JBFundingCycle memory) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project that is being withdrawn from.
  * `_amount` is the amount being withdrawn.
* Through the [`onlyTerminal`](../../../../../../protocol/specifications/contracts/or-controllers/jbcontrollerutility/modifiers/onlyterminal.md) modifier, the function can only be accessed by a terminal of the `_projectId`.
* The function overrides a function definition from the [`IJBController`](../../../../../../protocol/interfaces/ijbcontroller.md) interface.
* The function doesn't return anything.

## Body

1.  Tap from the project's funding cycle.

    ```solidity
    // Tap from the project's funding cycle.
    return fundingCycleStore.tapFrom(_projectId, _amount);
    ```

    _External references:_

    * [`tapFrom`](../../../jbfundingcyclestore/write/tapfrom.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Signals that a project's funds are being withdrawn.

  @dev
  Only a project's terminal can signal a withdrawal.

  @param _projectId The ID of the project that is being withdrawn from.
  @param _amount The amount being withdrawn.
*/
function signalWithdrawlFrom(uint256 _projectId, uint256 _amount)
  external
  override
  onlyTerminal(_projectId)
  returns (JBFundingCycle memory)
{
  // Tap from the project's funding cycle.
  return fundingCycleStore.tapFrom(_projectId, _amount);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                  | Description                                                                   |
| ----------------------- | ----------------------------------------------------------------------------- |
| **`0x0f: SOME_LOCKED`** | Thrown if the splits that are being set override some splits that are locked. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                                                                 | Data                                                                                                                                                                                                                 |
| -------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetSplit`**](../../../../../../protocol/specifications/contracts/or-controllers/jbcontroller/events/setsplit.md) | <ul><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed domain</code></li><li><code>uint256 indexed group</code></li><li><code>Split split</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
