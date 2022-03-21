# reclaimableOverflowOf

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**The amount of overflowed tokens from a terminal that can be reclaimed by the specified number of tokens.**

_If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used._

_The reclaimable overflow is returned in terms of the specified terminal's currency._

_The reclaimable overflow is represented as a fixed point number with the same amount of decimals as the specified terminal._

#### Definition

```solidity
function reclaimableOverflowOf(
  IJBPaymentTerminal _terminal,
  uint256 _projectId,
  uint256 _tokenCount
) external view override returns (uint256) { ... }
```

* Arguments:
  * `_terminal` is the terminal for which the overflow is being calculated.
  * `_projectId` is the ID of the project to get the reclaimable overflow amount for.
  * `_tokenCount` is the number of tokens to make the calculation with, as a fixed point number with 18 decimals.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The function returns the amount of overflowed tokens that can be reclaimed.

#### Body

1.  Forward the call to the internal version of the function that is also used by other operations.

    ```solidity
    _reclaimableOverflowOf(
      _terminal,
      _projectId,
      fundingCycleStore.currentOf(_projectId),
      _tokenCount,
      _terminal.decimals(),
      _terminal.currency()
    );
    ```

    _Internal references:_

    * [`_reclaimableOverflowOf`](\_reclaimableoverflowof.md)

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
    * [`decimals`](../../../TODO)
    * [`currency`](../../../TODO)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  The amount of overflowed tokens from a terminal that can be reclaimed by the specified number of tokens.

  @dev 
  If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used.

  @dev
  The reclaimable overflow is returned in terms of the specified terminal's currency.

  @dev
  The reclaimable overflow is represented as a fixed point number with the same amount of decimals as the specified terminal.

  @param _terminal The terminal for which the overflow is being calculated.
  @param _projectId The ID of the project to get the reclaimable overflow amount for.
  @param _tokenCount The number of tokens to make the calculation with, as a fixed point number with 18 decimals.

  @return The amount of overflowed tokens that can be reclaimed.
*/
function reclaimableOverflowOf(
  IJBPaymentTerminal _terminal,
  uint256 _projectId,
  uint256 _tokenCount
) external view override returns (uint256) {
  return
    _reclaimableOverflowOf(
      _terminal,
      _projectId,
      fundingCycleStore.currentOf(_projectId),
      _tokenCount,
      _terminal.decimals(),
      _terminal.currency()
    );
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
