# tapFrom

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Tap funds from a project's currently tappable funding cycle.**

_Only a project's current controller can tap funds for its funding cycles._

# Definition

```solidity
function tapFrom(uint256 _projectId, uint256 _amount)
  external
  override
  onlyController(_projectId)
  returns (JBFundingCycle memory) { ... }
```

* `_projectId` is the ID of the project being tapped.
* `_amount` is the amount being tapped.
* Through the [`onlyController`](../../jbcontrollerutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`.
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* Returns the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) that was tapped.

# Body

1.  Check that the amount is positive.

    ```solidity
    // Amount must be positive.
    require(_amount > 0, '0x1a: INSUFFICIENT_FUNDS');
    ```
2.  Find the ID of the funding cycle that should be tapped.

    ```solidity
    // Get a reference to the funding cycle being tapped.
    uint256 _fundingCycleId = _tappableOf(_projectId);
    ```

    _Internal references:_

    * [`_tappableOf`](\_tappableof.md)
3.  Get a reference to the new amount that will be tapped. This is the sum of the provided `_amount` with what has already been tapped for the funding cycle.

    ```solidity
    // The new amount that has been tapped.
    uint256 _newTappedAmount = _tappedAmountOf[_fundingCycleId] + _amount;
    ```

    _Internal references:_

    * [`_tappedAmountOf`](../properties/\_tappedamountof.md)
4.  The amount being tapped plus what's already been tapped must be within the configured target for the funding cycle.

    ```solidity
    // Amount must be within what is still tappable.
    require(_newTappedAmount <= _targetOf[_fundingCycleId], '0x1b: INSUFFICIENT_FUNDS');
    ```
5.  Store the new tapped amount.

    ```solidity
    // Store the new amount.
    _tappedAmountOf[_fundingCycleId] = _newTappedAmount;
    ```
6.  Emit a `Tap` event with the relevant parameters.

    ```solidity
    emit Tap(_fundingCycleId, _projectId, _amount, _newTappedAmount, msg.sender);
    ```

    _Event references:_

    * [`Tap`](../events/tap.md)
7.  Return the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) struct that has been tapped.

    ```solidity
    return _getStructFor(_fundingCycleId);
    ```

    _Internal references:_

    * [`_getStructFor`](../read/\_getstructfor.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Tap funds from a project's currently tappable funding cycle.

  @dev
  Only a project's current controller can tap funds for its funding cycles.

  @param _projectId The ID of the project being tapped.
  @param _amount The amount being tapped.

  @return The tapped funding cycle.
*/
function tapFrom(uint256 _projectId, uint256 _amount)
  external
  override
  onlyController(_projectId)
  returns (JBFundingCycle memory)
{
  // Amount must be positive.
  require(_amount > 0, '0x1a: BAD_AMOUNT');
  
  // Get a reference to the funding cycle being tapped.
  uint256 _fundingCycleId = _tappableOf(_projectId);

  // The new amount that has been tapped.
  uint256 _newTappedAmount = _tappedAmountOf[_fundingCycleId] + _amount;

  // Amount must be within what is still tappable.
  require(_newTappedAmount <= _targetOf[_fundingCycleId], '0x1b: INSUFFICIENT_FUNDS');

  // Store the new amount.
  _tappedAmountOf[_fundingCycleId] = _newTappedAmount;

  emit Tap(_fundingCycleId, _projectId, _amount, _newTappedAmount, msg.sender);

  return _getStructFor(_fundingCycleId);
}
```
{% endtab %}

{% tab title="Errors" %}
| String                         | Description                                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| **`0x1a: BAD_AMOUNT`**         | Thrown if the provided `_amount` is 0.                                                                              |
| **`0x1b: INSUFFICIENT_FUNDS`** | Thrown if tapping the `_amount` provided would result in tapping an amount greater than the funding cycle's target. |
{% endtab %}

{% tab title="Events" %}
| Name                            | Data                                                                                                                                                                                                                              |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`Tap`**](../events/tap.md)   | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 amount</code></li><li><code>uint256 newTappedAmount</code></li><li><code>address caller</code></li></ul> |
| [**`Init`**](../events/init.md) | <ul><li><code>uint256 indexed fundingCycleId</code></li><li><code>uint256 indexed projectId</code></li><li><code>uint256 indexed basedOn</code></li></ul>                                                                         |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
