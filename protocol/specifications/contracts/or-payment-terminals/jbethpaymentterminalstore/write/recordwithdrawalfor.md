# recordWithdrawalFor

Contract: [`JBETHPaymentTerminalStore`](./)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly withdrawn funds for a project.**

_Only the associated payment terminal can record a used allowance._

## Definition

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
  * `_projectId` The ID of the project that is having funds withdrawn.
  * `_amount` The amount being withdrawn. Send as wei (18 decimals).
  * `_currency` The expected currency of the `_amount` being tapped. This must match the project's current funding cycle's currency.
  * `_minReturnedWei` The minimum number of wei that should be withdrawn.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns:
  * `fundingCycle` is the funding cycle during which the withdrawal was made.
  * `withdrawnAmount` is the amount withdrawn.

## Body

1.  Tell the project's controller that an amount is being withdrawn. Get the [`JBFundingCycle`](../../../../../data-structures/jbfundingcycle.md) data struct during which the withdraw was made.

    ```solidity
    // Registers the funds as withdrawn and gets the ID of the funding cycle during which this withdrawal is being made.
    fundingCycle = directory.controllerOf(_projectId).signalWithdrawlFrom(_projectId, _amount);
    ```

    _External references:_

    * [`signalWithdrawlFrom`](../../../or-controllers/jbcontroller/write/signalwithdrawlfrom.md)
2.  Make the sure the project has a funding cycle by checking if the returned number is non-zero.

    ```solidity
    // Funds cannot be withdrawn if there's no funding cycle.
    require(fundingCycle.id > 0, '0x3d: NOT_FOUND');
    ```
3.  Make sure the project's current funding cycle isn't configured to pause withdrawals.

    ```solidity
    // The funding cycle must not be configured to have withdrawals paused.
    require(!fundingCycle.withdrawalsPaused(), '0x3e: PAUSED');
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.withdrawalsPaused(...)`
4.  Make the sure provided currency matches the funding cycle's currency.

    ```solidity
    // Make sure the currencies match.
    require(_currency == fundingCycle.currency, '0x3f: UNEXPECTED_CURRENCY');
    ```
5.  Convert the amount to ETH.

    ```solidity
    // Convert the amount to wei.
    withdrawnAmount = PRBMathUD60x18.div(
      _amount,
      prices.priceFor(fundingCycle.currency, JBCurrencies.ETH)
    );
    ```

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.div`
6.  Make sure the project has enough of a balance to withdraw the desired amount.

    ```solidity
    // The amount being withdrawn must be available.
    require(withdrawnAmount <= balanceOf[_projectId], '0x40: INSUFFICIENT_FUNDS');
    ```
7.  Make sure the there is at least as much wei being withdrawn as expected.

    ```solidity
    // The amount being withdrawn must be at least as much as was expected.
    require(_minReturnedWei <= withdrawnAmount, '0x41: INADEQUATE');
    ```
8.  Store the decremented balance.

    ```solidity
    // Removed the withdrawn funds from the project's balance.
    balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
    ```
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly withdrawn funds for a project.

  @dev
  Only the associated payment terminal can record a withdrawal.

  @param _projectId The ID of the project that is having funds withdrawn.
  @param _amount The amount being withdrawn. Send as wei (18 decimals).
  @param _currency The expected currency of the `_amount` being tapped. This must match the project's current funding cycle's currency.
  @param _minReturnedWei The minimum number of wei that should be withdrawn.

  @return fundingCycle The funding cycle during which the withdrawal was made.
  @return withdrawnAmount The amount withdrawn.
*/
function recordWithdrawalFor(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei
)
  external
  onlyAssociatedPaymentTerminal
  returns (JBFundingCycle memory fundingCycle, uint256 withdrawnAmount)
{
  // Registers the funds as withdrawn and gets the ID of the funding cycle during which this withdrawal is being made.
  fundingCycle = directory.controllerOf(_projectId).signalWithdrawlFrom(_projectId, _amount);

  // Funds cannot be withdrawn if there's no funding cycle.
  require(fundingCycle.id > 0, '0x3d: NOT_FOUND');

  // The funding cycle must not be configured to have withdrawals paused.
  require(!fundingCycle.withdrawalsPaused(), '0x3e: PAUSED');

  // Make sure the currencies match.
  require(_currency == fundingCycle.currency, '0x3f: UNEXPECTED_CURRENCY');

  // Convert the amount to wei.
  withdrawnAmount = PRBMathUD60x18.div(
    _amount,
    prices.priceFor(fundingCycle.currency, JBCurrencies.ETH)
  );

  // The amount being withdrawn must be available.
  require(withdrawnAmount <= balanceOf[_projectId], '0x40: INSUFFICIENT_FUNDS');

  // The amount being withdrawn must be at least as much as was expected.
  require(_minReturnedWei <= withdrawnAmount, '0x41: INADEQUATE');

  // Removed the withdrawn funds from the project's balance.
  balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **`0x3d: NOT_FOUND`**           | Thrown if the project doesn't have a funding cycle.                                                               |
| **`0x3e: PAUSED`**              | Thrown if the project has configured its current funding cycle to pause withdrawals.                              |
| **`0x3f: UNEXPECTED_CURRENCY`** | Thrown if the currency of the specified amount doesn't match the currency of the project's current funding cycle. |
| **`0x41: NOT_ALLOWED`**         | Thrown if there isn't enough allowance for the specified terminal to fulfill the desired withdrawal.              |
| **`0x40: INSUFFICIENT_FUNDS`**  | Thrown if the project's balance isn't sufficient to fulfill the desired withdrawal.                               |
| **`0x41: INADEQUATE`**          | Thrown if the withdrawn amount is less than the minimum expected.                                                 |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
