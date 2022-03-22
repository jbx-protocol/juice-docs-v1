# currentReclaimableOverflowOf

Contract: [`JBPaymentTerminalStore`](../)​‌

Interface: [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md)

{% tabs %}
{% tab title="Step by step" %}
**The current amount of overflowed tokens from a terminal that can be reclaimed by the specified number of tokens.**

_If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used._

_The current reclaimable overflow is returned in terms of the specified terminal's currency._

_The reclaimable overflow is represented as a fixed point number with the same amount of decimals as the specified terminal._

#### Definition

```solidity
function currentReclaimableOverflowOf(
  IJBPaymentTerminal _terminal,
  uint256 _projectId,
  uint256 _tokenCount,
  bool _useLocalBalance
) external view override returns (uint256) { ... }
```

* Arguments:
  * `_terminal` is the terminal from which the reclaimable amount would come.
  * `_projectId` is the ID of the project to get the reclaimable overflow amount for.
  * `_tokenCount` is the number of tokens to make the calculation with, as a fixed point number with 18 decimals.
  * `_useLocalBalance` is a flag indicating whether the overflow used in the calculation should be limited to the overflow in the specified `_terminal`. If false, overflow is calculated from all of the project's terminals.
* The view function can be accessed externally by anyone.
* The view function does not alter state on the blockchain.
* The resulting function overrides a function definition from the [`JBPaymentTerminalStore`](../../../interfaces/ijbpaymentterminalstore.md) interface.
* The function returns the amount of overflowed tokens that can be reclaimed.

#### Body

1.  Get a reference to the project's current funding cycle.
    
    ```solidity
    // Get a reference to the project's current funding cycle.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)

2.  Get the amount of overflow to make the calculation with. Use the overflow of the provided terminal if a local balance should be used, otherwise use the total overflow of all of the project's terminals.

    ```solidity
    // Get the amount of current overflow.
    // Use the local overflow if the funding cycle specifies that it should be used. Otherwise use the project's total overflow across all of its terminals.
    uint256 _currentOverflow = _useLocalBalance
      ? _overflowDuring(
        IJBPaymentTerminal(msg.sender),
        _projectId,
        _fundingCycle,
        _terminal.currency()
      )
      : _currentTotalOverflowOf(_projectId, _terminal.decimals(), _terminal.currency());
    ```

    _Internal references:_

    * [`_overflowDuring`](\_overflowduring.md)
    * [`_currentTotalOverflowOf`](\_currenttotaloverflowof.md)

    _External references:_

    * [`decimals`](../../../TODO)
    * [`currency`](../../../TODO)

1.  Return the reclaimable overflow using the project's current funding cycle and the derived current overflow. If there's no current overflow, there's no reclaimable overflow.

    ```solidity
    // If there is no overflow, nothing is reclaimable.
    return
      _currentOverflow == 0
        ? 0
        : _reclaimableOverflowDuring(_projectId, _fundingCycle, _tokenCount, _currentOverflow);
    ```

    _Internal references:_

    * [`_reclaimableOverflowDuring`](\_reclaimableoverflowduring.md)

{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  The current amount of overflowed tokens from a terminal that can be reclaimed by the specified number of tokens.

  @dev 
  If the project has an active funding cycle reconfiguration ballot, the project's ballot redemption rate is used.

  @dev
  The current reclaimable overflow is returned in terms of the specified terminal's currency.

  @dev
  The reclaimable overflow is represented as a fixed point number with the same amount of decimals as the specified terminal.

  @param _terminal The terminal from which the reclaimable amount would come.
  @param _projectId The ID of the project to get the reclaimable overflow amount for.
  @param _tokenCount The number of tokens to make the calculation with, as a fixed point number with 18 decimals.
  @param _useLocalBalance A flag indicating whether the overflow used in the calculation should be limited to the overflow in the specified `_terminal`. If false, overflow is calculated from all of the project's terminals.

  @return The amount of overflowed tokens that can be reclaimed.
*/
function currentReclaimableOverflowOf(
  IJBPaymentTerminal _terminal,
  uint256 _projectId,
  uint256 _tokenCount,
  bool _useLocalBalance
) external view override returns (uint256) {
  // Get a reference to the project's current funding cycle.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Get the amount of current overflow.
  // Use the local overflow if the funding cycle specifies that it should be used. Otherwise use the project's total overflow across all of its terminals.
  uint256 _currentOverflow = _useLocalBalance
    ? _overflowDuring(
      IJBPaymentTerminal(msg.sender),
      _projectId,
      _fundingCycle,
      _terminal.currency()
    )
    : _currentTotalOverflowOf(_projectId, _terminal.decimals(), _terminal.currency());

  // If there is no overflow, nothing is reclaimable.
  return
    _currentOverflow == 0
      ? 0
      : _reclaimableOverflowDuring(_projectId, _fundingCycle, _tokenCount, _currentOverflow);
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
