# recordDistributionFor

Contract: [`JBETHPaymentTerminalStore`](../../../jbprices/write/)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly distributed funds for a project.**

_Only the associated payment terminal can record a used allowance._

### Definition

```solidity
function recordWithdrawalFor(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei
)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle, uint256 withdrawnAmount) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project that is having funds withdrawn.
  * `_amount` is the amount being distributed as a fixed point number.
  * `_currency` is the expected currency of the `_amount` being tapped. This must match the project's current funding cycle's currency.
  * `_minReturnedWei` is the minimum number of wei that should be withdrawn.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns:
  * `fundingCycle` is the funding cycle during which the withdrawal was made.
  * `distributedAmount` is the amount distribution in wei.

### Body

1.  Get a reference to the project's current funding cycle.

    ```solidity
    // Get a reference to the project's current funding cycle.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)
2.  Make sure the current funding cycle doesn't have distributions paused.

    ```solidity
    // The funding cycle must not be configured to have distributions paused.
    if (fundingCycle.distributionsPaused()) {
      revert FUNDING_CYCLE_DISTRIBUTION_PAUSED();
    }
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.distributionsPaused(...)`
3.  Make the sure the provided currency matches the expected currency for the distribution limit.

    ```solidity
    // Make sure the currencies match.
    if (
      _currency !=
      directory.controllerOf(_projectId).distributionLimitCurrencyOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      )
    ) {
      revert CURRENCY_MISMATCH();
    }
    ```

    _External references:_

    * [`distributionLimitCurrencyOf`](../../../or-controllers/jbcontroller/read/distributionLimitCurrencyof.md)
4.  Calculate the new total amount that has been distributed during this funding cycle by adding the amount being distributed to the used distribution limit.

    ```solidity
    // The new total amount that has been distributed during this funding cycle.
    uint256 _newUsedDistributionLimitOf = usedDistributionLimitOf[_projectId][fundingCycle.number] +
      _amount;
    ```

    _Internal references:_

    * [`usedDistributionLimitOf`](../../../jbfundingcyclestore/read/useddistributionlimitof.md)
5.  Make sure the new total amount distributed will be at most the distribution limit.

    ```solidity
    // Amount must be within what is still distributable.
    if (
      _newUsedDistributionLimitOf >
      directory.controllerOf(_projectId).distributionLimitOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      )
    ) {
      revert DISTRIBUTION_AMOUNT_LIMIT_REACHED();
    }
    ```

    _External references:_

    * [`distributionLimitOf`](../../../or-controllers/jbcontroller/read/distributionlimitof.md)
6.  Find the amount to distribute by converting the amount to ETH. If the currency is ETH, no conversion is necessary.

    ```solidity
    // Convert the amount to wei.
    distributedAmount = (_currency == JBCurrencies.ETH)
      ? _amount
      : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));
    ```

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.div`
7.  Make sure the project has access to the amount being distributed.

    ```solidity
    // The amount being distributed must be available.
    if (distributedAmount > balanceOf[_projectId]) {
      revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
    }
    ```

    _Internal references:_

    * [`balanceOf`](../../../jbfundingcyclestore/read/balanceof.md)
8.  Make sure there is at least as much wei being withdrawn as expected.

    ```solidity
    // The amount being distributed must be at least as much as was expected.
    if (_minReturnedWei > distributedAmount) {
      revert INADEQUATE_WITHDRAW_AMOUNT();
    }
    ```
9.  Store the new used distributed amount.

    ```solidity
    // Store the new amount.
    usedDistributionLimitOf[_projectId][fundingCycle.number] = _newUsedDistributionLimitOf;
    ```

    _Internal references:_

    * [`usedDistributionLimitOf`](../../../jbfundingcyclestore/read/useddistributionlimitof.md)
10. Store the decremented balance.

    ```solidity
    // Removed the distributed funds from the project's ETH balance.
    balanceOf[_projectId] = balanceOf[_projectId] - distributedAmount;
    ```

    _Internal references:_

    * [`balanceOf`](../../../jbfundingcyclestore/read/balanceof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly distributed funds for a project.

  @dev
  Only the associated payment terminal can record a distribution.

  @param _projectId The ID of the project that is having funds distributed.
  @param _amount The amount being distributed as a fixed point number.
  @param _currency The expected currency of the `_amount` being tapped. This must match the project's current funding cycle's currency.
  @param _minReturnedWei The minimum number of wei that should be distributed.

  @return fundingCycle The funding cycle during which the withdrawal was made.
  @return distributedAmount The amount distribution in wei.
*/
function recordDistributionFor(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei
)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle, uint256 distributedAmount)
{
  // Get a reference to the project's current funding cycle.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // The funding cycle must not be configured to have distributions paused.
  if (fundingCycle.distributionsPaused()) {
    revert FUNDING_CYCLE_DISTRIBUTION_PAUSED();
  }

  // Make sure the currencies match.
  if (
    _currency !=
    directory.controllerOf(_projectId).distributionLimitCurrencyOf(
      _projectId,
      fundingCycle.configuration,
      terminal
    )
  ) {
    revert CURRENCY_MISMATCH();
  }

  // The new total amount that has been distributed during this funding cycle.
  uint256 _newUsedDistributionLimitOf = usedDistributionLimitOf[_projectId][fundingCycle.number] +
    _amount;

  // Amount must be within what is still distributable.
  if (
    _newUsedDistributionLimitOf >
    directory.controllerOf(_projectId).distributionLimitOf(
      _projectId,
      fundingCycle.configuration,
      terminal
    )
  ) {
    revert DISTRIBUTION_AMOUNT_LIMIT_REACHED();
  }

  // Convert the amount to wei.
  distributedAmount = (_currency == JBCurrencies.ETH)
    ? _amount
    : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));

  // The amount being distributed must be available.
  if (distributedAmount > balanceOf[_projectId]) {
    revert INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE();
  }

  // The amount being distributed must be at least as much as was expected.
  if (_minReturnedWei > distributedAmount) {
    revert INADEQUATE_WITHDRAW_AMOUNT();
  }

  // Store the new amount.
  usedDistributionLimitOf[_projectId][fundingCycle.number] = _newUsedDistributionLimitOf;

  // Removed the distributed funds from the project's ETH balance.
  balanceOf[_projectId] = balanceOf[_projectId] - distributedAmount;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                                          | Description                                                                                                          |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **`FUNDING_CYCLE_DISTRIBUTION_PAUSED`**         | Thrown if the project has configured its current funding cycle to pause distributions.                               |
| **`CURRENCY_MISMATCH`**                         | Thrown if the currency of the specified amount doesn't match the currency of the project's current funding cycle.    |
| **`DISTRIBUTION_AMOUNT_LIMIT_REACHED`**         | Thrown if there isn't enough of a distribution limit for the specified terminal to fulfill the desired distribution. |
| **`INADEQUATE_PAYMENT_TERMINAL_STORE_BALANCE`** | Thrown if the project's balance isn't sufficient to fulfill the desired distribution.                                |
| **`INADEQUATE_WITHDRAW_AMOUNT`**                | Thrown if the distribution amount is less than the minimum expected.                                                 |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
