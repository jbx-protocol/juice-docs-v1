# recordUsedAllowanceOf

Contract: [`JBPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly used allowance funds of a project.**

_The msg.sender must be an [`IJBPaymentTerminal`](../../../interfaces/ijbpaymentterminal.md)._

#### Definition

```solidity
function recordUsedAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _balanceCurrency
)
  external
  override
  nonReentrant
  returns (JBFundingCycle memory fundingCycle, uint256 usedAmount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to use the allowance of.
  * `_amount` is the amount to use from the allowance, as a fixed point number. 
  * `_currency` is the currency of the `_amount`. Must match the currency of the overflow allowance.
  * `_balanceCurrency` is the currency that the balance is expected to be in terms of.
* The function returns:
  * `fundingCycle` is the funding cycle during which the withdrawal was made.
  * `usedAmount` is the amount of terminal tokens used, as a fixed point number with the same amount of decimals as its relative terminal.

#### Body

1.  Get a reference to the project's first funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../jbfundingcyclestore/read/currentof.md)
2.  Get a reference to the new used overflow allowance.

    ```solidity
    // Get a reference to the new used overflow allowance.
    uint256 _newUsedOverflowAllowanceOf = usedOverflowAllowanceOf[IJBPaymentTerminal(msg.sender)][
      _projectId
    ][fundingCycle.configuration] + _amount;
    ```

    _Internal references:_

    * [`usedOverflowAllowanceOf`](../properties/usedoverflowallowanceof.md)
3.  Get a reference to the overflow allowance of the project during the current funding cycle configuration, and the currency the overflow allowance is in terms of.

    ```solidity
    // There must be sufficient allowance available.
    (uint256 _overflowAllowanceOf, uint256 _overflowAllowanceCurrency) = directory
      .controllerOf(_projectId)
      .overflowAllowanceOf(_projectId, fundingCycle.configuration, IJBPaymentTerminal(msg.sender));
    ```

    _External references:_

    * [`controllerOf`](../../jbdirectory/properties/controllerof.md)
    * [`overflowAllowanceOf`](../../or-controllers/jbcontroller/read/overflowallowanceof.md)
4.  Make sure there's enough allowance left to accomodate the new used amount.

    ```solidity
    if (_newUsedOverflowAllowanceOf > _overflowAllowanceOf || _overflowAllowanceOf == 0)
      revert INADEQUATE_CONTROLLER_ALLOWANCE();
    ```

5.  Make the sure the provided currency matches the expected currency for the overflow allowance.

    ```solidity
    // Make sure the currencies match.
    if (_currency != _overflowAllowanceCurrency) revert CURRENCY_MISMATCH();
    ```

6.  Get a reference to the current distribution limit of the project during the current funding cycle configuration.

    ```solidity
    // Get the current funding target
    uint256 distributionLimit =
      directory.controllerOf(_projectId).distributionLimitOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      );
    ```

    _External references:_

    * [`controllerOf`](../../../jbdirectory/properties/controllerof.md)
    * [`distributionLimitOf`](../../../or-controllers/jbcontroller/read/distributionlimitof.md)

7.  Calculate how much of the balance will be used. If the currency of the allowance and the balance are the same, no price conversion is necessary. Otherwise, convert the allowance currency to that of the balance. 

    ```solidity
    // Convert the amount to this store's terminal's token.
    usedAmount = (_currency == _balanceCurrency)
      ? _amount
      : PRBMath.mulDiv(
        _amount,
        10**_MAX_FIXED_POINT_FIDELITY, // Use _MAX_FIXED_POINT_FIDELITY to keep as much of the `_amount.value`'s fidelity as possible when converting.
        prices.priceFor(_currency, _balanceCurrency, _MAX_FIXED_POINT_FIDELITY)
      );
    ```

    _Libraries used:_

    * [`PRBMath`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMath.sol)
      * `.mulDiv(...)`

    _Internal references:_

    * [`_MAX_FIXED_POINT_FIDELITY`](../properties/_max_fixed_point_fidelity.md)

    _External references:_

    * [`priceFor`](../../jbprices/read/pricefor.md)

8.  Make sure the amount being used is available in overflow.

    ```solidity
    // The amount being withdrawn must be available in the overflow.
    if (
      usedAmount >
      _overflowDuring(IJBPaymentTerminal(msg.sender), _projectId, fundingCycle, _balanceCurrency)
    ) revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
    ```

    _Internal references:_

    * [`_overflowDuring`](../read/_overflowduring.md)
9.  Store the incremented value that tracks how much of a project's allowance was used during the current funding cycle configuration.

    ```solidity
    // Store the incremented value.
    usedOverflowAllowanceOf[IJBPaymentTerminal(msg.sender)][_projectId][
      fundingCycle.configuration
    ] = _newUsedOverflowAllowanceOf;
    ```

    _Internal references:_

    * [`usedOverflowAllowanceOf`](../properties/usedoverflowallowanceof.md)
10. Store the decremented balance.

    ```solidity
    // Update the project's balance.
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
      balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] -
      usedAmount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly used allowance funds of a project.

  @dev
  The msg.sender must be an IJBPaymentTerminal. 

  @param _projectId The ID of the project to use the allowance of.
  @param _amount The amount to use from the allowance, as a fixed point number. 
  @param _currency The currency of the `_amount`. Must match the currency of the overflow allowance.
  @param _balanceCurrency The currency that the balance is expected to be in terms of.

  @return fundingCycle The funding cycle during which the overflow allowance is being used.
  @return usedAmount The amount of terminal tokens used, as a fixed point number with the same amount of decimals as its relative terminal.
*/
function recordUsedAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _balanceCurrency
)
  external
  override
  nonReentrant
  returns (JBFundingCycle memory fundingCycle, uint256 usedAmount)
{
  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Get a reference to the new used overflow allowance.
  uint256 _newUsedOverflowAllowanceOf = usedOverflowAllowanceOf[IJBPaymentTerminal(msg.sender)][
    _projectId
  ][fundingCycle.configuration] + _amount;

  // There must be sufficient allowance available.
  (uint256 _overflowAllowanceOf, uint256 _overflowAllowanceCurrency) = directory
    .controllerOf(_projectId)
    .overflowAllowanceOf(_projectId, fundingCycle.configuration, IJBPaymentTerminal(msg.sender));

  if (_newUsedOverflowAllowanceOf > _overflowAllowanceOf || _overflowAllowanceOf == 0)
    revert INADEQUATE_CONTROLLER_ALLOWANCE();

  // Make sure the currencies match.
  if (_currency != _overflowAllowanceCurrency) revert CURRENCY_MISMATCH();

  // Convert the amount to this store's terminal's token.
  usedAmount = (_currency == _balanceCurrency)
    ? _amount
    : PRBMath.mulDiv(
      _amount,
      10**_MAX_FIXED_POINT_FIDELITY, // Use _MAX_FIXED_POINT_FIDELITY to keep as much of the `_amount.value`'s fidelity as possible when converting.
      prices.priceFor(_currency, _balanceCurrency, _MAX_FIXED_POINT_FIDELITY)
    );

  // The amount being withdrawn must be available in the overflow.
  if (
    usedAmount >
    _overflowDuring(IJBPaymentTerminal(msg.sender), _projectId, fundingCycle, _balanceCurrency)
  ) revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();

  // Store the incremented value.
  usedOverflowAllowanceOf[IJBPaymentTerminal(msg.sender)][_projectId][
    fundingCycle.configuration
  ] = _newUsedOverflowAllowanceOf;

  // Update the project's balance.
  balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] =
    balanceOf[IJBPaymentTerminal(msg.sender)][_projectId] -
    usedAmount;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                                          | Description                                                                                                       |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **`CURRENCY_MISMATCH`**                         | Thrown if the currency of the specified amount doesn't match the currency of the project's current funding cycle. |
| **`INADEQUATE_CONTROLLER_ALLOWANCE`**           | Thrown if there isn't enough allowance for the specified terminal to fulfill the desired withdrawal.              |
| **`INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE`** | Thrown if the project's balance isn't sufficient to fulfill the desired withdrawal.                               |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
