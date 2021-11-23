# recordUsedAllowanceOf

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly used allowance funds of a project.**

_Only the associated payment terminal can record a used allowance._

# Definition

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
  * `_amount` is the amount of the allowance to use.
  * `_currency` is the currency of the `_amount` value. Must match the funding cycle's currency.
  * `_minReturnedWei` is the amount of wei that is expected to be withdrawn.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns:
  * `fundingCycle` is the funding cycle during which the withdrawal was made.
  * `withdrawnAmount` is the amount withdrawn.

# Body

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
    require(
      _currency ==
        directory.controllerOf(_projectId).currencyOf(
          _projectId,
          fundingCycle.configuration,
          terminal
        ),
      '0x42: UNEXPECTED_CURRENCY'
    );
    ```

    _External references:_

    * [`currencyOf`](../../../or-controllers/jbcontroller/read/currencyof.md)
3.  Find the amount to withdraw by converting the amount to ETH. If the currency is 0, it is assumed that the currency is the same as the token being withdrawn so no conversion is necessary.

    ```solidity
    // Convert the amount to wei.
    // A currency of 0 should be interpreted as whatever the currency being withdrawn is.
    withdrawnAmount = _currency == 0
      ? _amount
      : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));
    ```

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.div`
4.  Make sure the there is enough allowance to use.

    ```solidity
    // There must be sufficient allowance available.
    require(
      withdrawnAmount <=
        directory.controllerOf(_projectId).overflowAllowanceOf(
          _projectId,
          fundingCycle.configuration,
          terminal
        ) -
          usedOverflowAllowanceOf[_projectId][fundingCycle.configuration],
      '0x43: NOT_ALLOWED'
    );
    ```

    _Internal references:_

    * [`usedOverflowAllowanceOf`](../properties/usedoverflowallowanceof.md)

    _External references:_

    * [`controllerOf`](../../../jbdirectory/properties/controllerof.md)
    * [`overflowAllowanceOf`](../../../or-controllers/jbcontroller/properties/overflowallowanceof.md)
5.  Make sure the project has enough of a balance to use the desired amount.

    ```solidity
    // The amount being withdrawn must be available.
    require(withdrawnAmount <= balanceOf[_projectId], '0x44: INSUFFICIENT_FUNDS');
    ```
6.  Make sure the there is at least as much wei being returned as expected.

    ```solidity
    // The amount being withdrawn must be at least as much as was expected.
    require(_minReturnedWei <= withdrawnAmount, '0x45: INADEQUATE');
    ```
7.  Store the incremented value that tracks how much of a project's allowance was used during the current funding cycle configuration.

    ```solidity
    // Store the decremented value.
    usedOverflowAllowanceOf[_projectId][fundingCycle.configuration] =
      usedOverflowAllowanceOf[_projectId][fundingCycle.configuration] +
      withdrawnAmount;
    ```
8.  Store the decremented balance.

    ```solidity
    // Update the project's balance.
    balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/** 
  @notice 
  Records newly used allowance funds of a project.

  @dev
  Only the associated payment terminal can record a used allowance.

  @param _projectId The ID of the project to use the allowance of.
  @param _amount The amount of the allowance to use.
  @param _currency The currency of the `_amount` value. Must match the funding cycle's currency.
  @param _minReturnedWei The amount of wei that is expected to be withdrawn.

  @return fundingCycle The funding cycle during which the withdrawal is being made.
  @return withdrawnAmount The amount withdrawn.
*/
function recordUsedAllowanceOf(
  uint256 _projectId,
  IJBTerminal _terminal,
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
  require(
    _currency ==
      directory.controllerOf(_projectId).currencyOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      ),
    '0x42: UNEXPECTED_CURRENCY'
  );

  // Convert the amount to wei.
  // A currency of 0 should be interpreted as whatever the currency being withdrawn is.
  withdrawnAmount = _currency == 0
    ? _amount
    : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));

  // There must be sufficient allowance available.
  require(
    withdrawnAmount <=
      directory.controllerOf(_projectId).overflowAllowanceOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      ) -
        usedOverflowAllowanceOf[_projectId][fundingCycle.configuration],
    '0x43: NOT_ALLOWED'
  );

  // The amount being withdrawn must be available.
  require(withdrawnAmount <= balanceOf[_projectId], '0x44: INSUFFICIENT_FUNDS');

  // The amount being withdrawn must be at least as much as was expected.
  require(_minReturnedWei <= withdrawnAmount, '0x45: INADEQUATE');

  // Store the decremented value.
  usedOverflowAllowanceOf[_projectId][fundingCycle.configuration] =
    usedOverflowAllowanceOf[_projectId][fundingCycle.configuration] +
    withdrawnAmount;

  // Update the project's balance.
  balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **`0x42: UNEXPECTED_CURRENCY`** | Thrown if the currency of the specified amount doesn't match the currency of the project's current funding cycle. |
| **`0x43: NOT_ALLOWED`**         | Thrown if there isn't enough allowance for the specified terminal to fulfill the desired withdrawal.              |
| **`0x44: INSUFFICIENT_FUNDS`**  | Thrown if the project's balance isn't sufficient to fulfill the desired withdrawal.                               |
| **`0x45: INADEQUATE`**          | Thrown if the withdrawn amount is less than the minimum expected.                                                 |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
