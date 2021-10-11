# tapFrom

Contract:[`JBFundingCycleStore`](../)​‌

Interface: `IJBFundingCycleStore`

{% tabs %}
{% tab title="Step by step" %}
**Tap funds from a project's currently tappable funding cycle.**

_Only a project's current controller can tap funds for its funding cycles._\
\
Definition:

```solidity
function tapFrom(uint256 _projectId, uint256 _amount)
  external
  override
  onlyController(_projectId)
  returns (JBFundingCycle memory) { ... }
```

* `_projectId` is the ID of the project being tapped.
* `_amount` is the amount being tapped.
* Through the [`onlyController`](../../jbutility/modifiers/onlycontroller.md) modifier, the function can only be accessed by the controller of the `_projectId`. 
* The function overrides a function definition from the `IJBFundingCycleStore` interface.
* Returns the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) that was tapped.



1.  Check that the amount is positive.

    ```javascript
    // Amount must be positive.
    require(_amount > 0, '0x1a: INSUFFICIENT_FUNDS');
    ```


2.  Find the ID of the funding cycle that should be tapped.\


    _Internal references:_

    * [`_tappableOf`](\_tappableof.md)

    ```javascript
    // Get a reference to the funding cycle being tapped.
    uint256 _fundingCycleId = _tappableOf(_projectId);
    ```


3.  Get a reference to the new amount that will be tapped. This is the sum of the provided `_amount` with what has already been tapped for the funding cycle.\


    _Internal references:_

    * [`_tappedAmountOf`](../properties/\_tappedamountof.md)

    ```javascript
    // The new amount that has been tapped.
    uint256 _newTappedAmount = _tappedAmountOf[_fundingCycleId] + _amount;
    ```


4.  The amount being tapped plus what's already been tapped must be within the configured target for the funding cycle.

    ```javascript
    // Amount must be within what is still tappable.
    require(_newTappedAmount <= _targetOf[_fundingCycleId], '0x1b: INSUFFICIENT_FUNDS');
    ```


5.  Store the new tapped amount.

    ```javascript
    // Store the new amount.
    _tappedAmountOf[_fundingCycleId] = _newTappedAmount;
    ```


6.  Emit a `Tap` event with the all relevant parameters. \


    _Event references:_

    * [`Tap`](../events/tap.md) 

    ```javascript
    emit Tap(_fundingCycleId, _projectId, _amount, _newTappedAmount, msg.sender);
    ```


7.  Return the [`JBFundingCycle`](../../../data-structures/jbfundingcycle.md) struct that has been tapped.\


    _Internal references:_

    * [`_getStructFor`](../read/\_getstructfor.md)

    ```javascript
    return _getStructFor(_fundingCycleId);
    ```
{% endtab %}

{% tab title="Only code" %}
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
| Name       | Data                                                                                                                                                                                                                                                                                         |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`Tap`**  | <ul><li><code>uint256 indexed fundingCycleId</code> </li><li><code>uint256 indexed projectId</code> </li><li><code>uint256 amount</code> </li><li><code>uint256 newTappedAmount</code> </li><li><code>address caller</code></li></ul><p><code></code><a href="../events/tap.md">more</a></p> |
| **`Init`** | <ul><li><code>uint256 indexed fundingCycleId</code> </li><li><code>uint256 indexed projectId</code> </li><li><code>uint256 indexed basedOn</code></li></ul><p><a href="../events/init.md">more</a></p>                                                                                       |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
