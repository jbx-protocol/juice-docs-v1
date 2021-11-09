# recordDistributionFor

Contract: [`JBETHPaymentTerminalStore`](../../../jbfundingcyclestore/write/)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly distributed funds for a project.**

_Only the associated payment terminal can record a used allowance._

# Definition

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

# Body

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
    require(!fundingCycle.distributionsPaused(), '0x3e: PAUSED');
    ```

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.distributionsPaused(...)`

3.  Make the sure the provided currency matches the expected currency for the distribution limit.

    ```solidity
    // Make sure the currencies match.
    require(
      _currency ==
        directory.controllerOf(_projectId).currencyOf(
          _projectId,
          fundingCycle.configuration,
          terminal
        ),
      '0x3f: UNEXPECTED_CURRENCY'
    );
    ```

    _External references:_

    * [`currencyOf`](../../../or-controllers/jbcontroller/read/currencyof.md)

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
    require(
      _newUsedDistributionLimitOf <=
        directory.controllerOf(_projectId).distributionLimitOf(
          _projectId,
          fundingCycle.configuration,
          terminal
        ),
      '0x1b: INSUFFICIENT_FUNDS'
    );
    ```

    _External references:_

    * [`distributionLimitOf`](../../../or-controllers/jbcontroller/read/distributionlimitof.md)

6.  Find the amount to distribute by converting the amount to ETH. If the currency is 0, it is assumed that the currency is the same as the token being withdrawn so no conversion is necessary.

    ```solidity
    // Convert the amount to wei.
    // A currency of 0 should be interpreted as whatever the currency being distributed is.
    distributedAmount = _currency == 0
      ? _amount
      : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));
    ```

    _Libraries used:_

    * [`PRBMathUD60x18`](https://github.com/hifi-finance/prb-math/blob/main/contracts/PRBMathUD60x18.sol)
      * `.div`

7.  Make sure the project has access to the amount being distributed.

    ```solidity
    // The amount being distributed must be available.
    require(distributedAmount <= balanceOf[_projectId], '0x40: INSUFFICIENT_FUNDS');
    ```

    _Internal references:_

    * [`balanceOf`](../../../jbfundingcyclestore/read/balanceof.md)

8.  Make sure there is at least as much wei being withdrawn as expected.

    ```solidity
    // The amount being distributed must be at least as much as was expected.
    require(_minReturnedWei <= distributedAmount, '0x41: INADEQUATE');
    ```

9.  Store the new used distributed amount.

    ```solidity
    // Store the new amount.
    usedDistributionLimitOf[_projectId][fundingCycle.number] = _newUsedDistributionLimitOf;
    ```

    _Internal references:_

    * [`usedDistributionLimitOf`](../../../jbfundingcyclestore/read/useddistributionlimitof.md)
10.  Store the decremented balance.

    ```solidity
    // Removed the withdrawn funds from the project's balance.
    balanceOf[_projectId] = balanceOf[_projectId] - withdrawnAmount;
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
  @param _amount The amount being distributed. Send as wei (18 decimals).
  @param _currency The expected currency of the `_amount` being tapped. This must match the project's current funding cycle's currency.
  @param _minReturnedWei The minimum number of wei that should be distributed.

  @return fundingCycle The funding cycle during which the distribution was made.
  @return amountDistribution The amount distributed.
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
  require(!fundingCycle.distributionsPaused(), '0x3e: PAUSED');

  // Make sure the currencies match.
  require(
    _currency ==
      directory.controllerOf(_projectId).currencyOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      ),
    '0x3f: UNEXPECTED_CURRENCY'
  );

  // The new total amount that has been distributed during this funding cycle.
  uint256 _newUsedDistributionLimitOf = usedDistributionLimitOf[_projectId][fundingCycle.number] +
    _amount;

  // Amount must be within what is still distributable.
  require(
    _newUsedDistributionLimitOf <=
      directory.controllerOf(_projectId).distributionLimitOf(
        _projectId,
        fundingCycle.configuration,
        terminal
      ),
    '0x1b: LIMIT_REACHED'
  );

  // Convert the amount to wei.
  // A currency of 0 should be interpreted as whatever the currency being distributed is.
  distributedAmount = _currency == 0
    ? _amount
    : PRBMathUD60x18.div(_amount, prices.priceFor(_currency, JBCurrencies.ETH));

  // The amount being distributed must be available.
  require(distributedAmount <= balanceOf[_projectId], '0x40: INSUFFICIENT_FUNDS');

  // The amount being distributed must be at least as much as was expected.
  require(_minReturnedWei <= distributedAmount, '0x41: INADEQUATE');

  // Store the new amount.
  usedDistributionLimitOf[_projectId][fundingCycle.number] = _newUsedDistributionLimitOf;

  // Removed the distributed funds from the project's balance.
  balanceOf[_projectId] = balanceOf[_projectId] - distributedAmount;
}
```
{% endtab %}

{% tab title="Errors" %}
| String                          | Description                                                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **`0x3e: PAUSED`**              | Thrown if the project has configured its current funding cycle to pause distributions.                              |
| **`0x3f: UNEXPECTED_CURRENCY`** | Thrown if the currency of the specified amount doesn't match the currency of the project's current funding cycle. |
| **`0x1b: LIMIT_REACHED`**         | Thrown if there isn't enough of a distribution limit for the specified terminal to fulfill the desired distribution.              |
| **`0x40: INSUFFICIENT_FUNDS`**  | Thrown if the project's balance isn't sufficient to fulfill the desired distribution.                               |
| **`0x41: INADEQUATE`**          | Thrown if the distribution amount is less than the minimum expected.                                                 |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
