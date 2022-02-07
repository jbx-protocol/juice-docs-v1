# recordUsedAllowanceOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly used allowance funds of a project.**

_Only the associated payment terminal can record a used allowance._

#### Definition

```solidity
function recordUsedAllowanceOf(
  uint256 _projectId,
  IJBTerminal _terminal,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei
)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle, uint256 withdrawnAmount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project to use the allowance of.
  * `_amount` is the amount of the allowance to use as a fixed point number.
  * `_currency` is the currency of the `_amount` value. Must match the funding cycle's currency.
  * `_minReturnedWei` is the amount of wei that is expected to be withdrawn.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns:
  * `fundingCycle` is the funding cycle during which the withdrawal was made.
  * `withdrawnAmount` is the amount withdrawn in wei.

#### Body

1.  Get a reference to the project's first funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
2.  Make the sure the provided currency matches the expected currency for the overflow allowance.

    ```solidity
    // Make sure the currencies match.
    if (
      _currency !=
      directory.controllerOf(_projectId).overflowAllowanceCurrencyOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      )
    ) {
      revert CURRENCY_MISMATCH();
    }
    ```

    _External references:_

    * [`overflowAllowanceCurrencyOf`](../../../or-controllers/jbcontroller/read/overflowallowanceurrencyof.md)
3.  Get a reference to the new used overflow allowance.

    ```solidity
    // Get a reference to the new used overflow allowance.
    uint256 _newUsedOverflowAllowanceOf = usedOverflowAllowanceOf[_projectId][
      fundingCycle.configuration
    ] + _amount;
    ```

    _Internal references:_

    * [`usedOverflowAllowanceOf`](../properties/usedoverflowallowanceof.md)
4.  Make sure there's enough allowance left to accomodate the new used amount.

    ```solidity
    // There must be sufficient allowance available.
    if (
      _newUsedOverflowAllowanceOf >
      directory.controllerOf(_projectId).overflowAllowanceOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      )
    ) {
      revert INADEQUATE_CONTROLLER_ALLOWANCE();
    }
    ```

    _External references:_

    * [`controllerOf`](../../../jbdirectory/properties/controllerof.md)
    * [`overflowAllowanceOf`](../../../or-controllers/jbcontroller/read/overflowallowanceof.md)
5.  Find the amount to withdraw by converting the amount to ETH. If the currency is ETH, no conversion is necessary.

    ```solidity
    // Convert the amount to wei.
    withdrawnAmount = (_currency == JBCurrencies.ETH)
      ? _amount
      : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));
    ```

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.div`
6.  Make sure the project has enough of a balance to use the desired amount.

    ```solidity
    // The amount being withdrawn must be available.
    if (withdrawnAmount > balanceOf[_projectId]) {
      revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
    }
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)
7.  Make sure the there is at least as much wei being returned as expected.

    ```solidity
    // The amount being withdrawn must be at least as much as was expected.
    if (_minReturnedWei > withdrawnAmount) {
      revert INADEQUATE_WITHDRAW_AMOUNT();
    }
    ```
8.  Store the incremented value that tracks how much of a project's allowance was used during the current funding cycle configuration.

    ```solidity
    // Store the incremented value.
    usedOverflowAllowanceOf[_projectId][fundingCycle.configuration] = _newUsedOverflowAllowanceOf;
    ```

    _Internal references:_

    * [`usedOverflowAllowanceOf`](../properties/usedoverflowallowanceof.md)
9.  Store the decremented balance.

    ```solidity
    // Update the project's ETH balance.
    balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
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
  Only the associated payment terminal can record a used allowance. 

  @param _projectId The ID of the project to use the allowance of.
  @param _amount The amount of the allowance to use as a fixed point number.
  @param _currency The currency of the `_amount` value. Must match the funding cycle's currency.
  @param _minReturnedWei The amount of wei that is expected to be withdrawn.

  @return fundingCycle The funding cycle during which the withdrawal is being made.
  @return withdrawnAmount The amount withdrawn in wei.
*/
function recordUsedAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei
)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle, uint256 withdrawnAmount)
{
  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // Make sure the currencies match.
  if (
    _currency !=
    directory.controllerOf(_projectId).overflowAllowanceCurrencyOf(
      _projectId,
      fundingCycle.configuration,
      terminal
    )
  ) {
    revert CURRENCY_MISMATCH();
  }

  // Get a reference to the new used overflow allowance.
  uint256 _newUsedOverflowAllowanceOf = usedOverflowAllowanceOf[_projectId][
    fundingCycle.configuration
  ] + _amount;

  // There must be sufficient allowance available.
  if (
    _newUsedOverflowAllowanceOf >
    directory.controllerOf(_projectId).overflowAllowanceOf(
      _projectId,
      fundingCycle.configuration,
      terminal
    )
  ) {
    revert INADEQUATE_CONTROLLER_ALLOWANCE();
  }

  // Convert the amount to wei.
  withdrawnAmount = (_currency == JBCurrencies.ETH)
    ? _amount
    : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));

  // The amount being withdrawn must be available.
  if (withdrawnAmount > balanceOf[_projectId]) {
    revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
  }

  // The amount being withdrawn must be at least as much as was expected.
  if (_minReturnedWei > withdrawnAmount) {
    revert INADEQUATE_WITHDRAW_AMOUNT();
  }

  // Store the incremented value.
  usedOverflowAllowanceOf[_projectId][fundingCycle.configuration] = _newUsedOverflowAllowanceOf;

  // Update the project's ETH balance.
  balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                                          | Description                                                                                                       |
| ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **`CURRENCY_MISMATCH`**                         | Thrown if the currency of the specified amount doesn't match the currency of the project's current funding cycle. |
| **`INADEQUATE_CONTROLLER_ALLOWANCE`**           | Thrown if there isn't enough allowance for the specified terminal to fulfill the desired withdrawal.              |
| **`INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE`** | Thrown if the project's balance isn't sufficient to fulfill the desired withdrawal.                               |
| **`INADEQUATE_WITHDRAW_AMOUNT`**                | Thrown if the withdrawn amount is less than the minimum expected.                                                 |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
