# recordPaymentFrom

Contract: [`JBETHPaymentTerminalStore`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Records newly contributed ETH to a project.**

_Mint's the project's tokens according to values provided by a configured data source. If no data source is configured, mints tokens proportional to the amount of the contribution._

_Only the associated payment terminal can record a payment._

# Definition

```solidity
function recordPaymentFrom(
  address _payer,
  uint256 _amount,
  uint256 _projectId,
  uint256 _preferClaimedTokensAndBeneficiary,
  uint256 _minReturnedTokens,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  onlyAssociatedPaymentTerminal
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 weight,
    uint256 tokenCount,
    string memory memo
  ) { ... }
```

* Arguments:
  * `_payer` is the original address that sent the payment to the terminal.
  * `_amount` is the amount that is being paid.
  * `_projectId` is the ID of the project being paid.
  * `_preferClaimedTokensAndBeneficiary` Two properties are included in this packed uint256:
    * The first bit contains the flag indicating whether the request prefers to issue tokens claimed as ERC-20s.
    * The remaining bits contains the address that should receive benefits from the payment.

    This design is necessary two prevent a "Stack too deep" compiler error that comes up if the variables are declared seperately.
  
  * `_minReturnedTokens` is the minimum number of tokens expected to be minted in return.
  * `_memo` is a memo that will be included in the published event.
  * `_delegateMetadata` are bytes to send along to the delegate, if one is used.
* Through the [`onlyAssociatedPaymentTerminal`](../modifiers/onlyassociatedpaymentterminal.md) modifier, the function is only accessible by the terminal that claimed this store.
* The function returns:
  * `fundingCycle` is the project's funding cycle during which payment was made.
  * `weight` is the weight according to which new token supply was minted.
  * `tokenCount` is the number of tokens that were minted.
  * `memo` is a memo that should be passed along to the emitted event.

# Body

1.  Get a reference to the project's current funding cycle that should be returned.

    ```solidity
    // Get a reference to the current funding cycle for the project.
    fundingCycle = fundingCycleStore.currentOf(_projectId);
    ```

    _External references:_

    * [`currentOf`](../../../jbfundingcyclestore/read/currentof.md)

2.  Make sure the project has a funding cycle configured. This is done by checking if the project's current funding cycle is non-zero.

    ```solidity
    // The project must have a funding cycle configured.
    require(fundingCycle.number > 0, '0x3a: NOT_FOUND');
    ```

3.  Make sure the project's funding cycle isn't configured to pause payments. 

    ```solidity
    // Must not be paused.
    require(!fundingCycle.payPaused(), '0x3b: PAUSED');
    ```

4.  Create a variable where a pay delegate will be saved if there is one. This pay delegate will later have it's method called if it exists.

    ```solidity
    // Save a reference to the delegate to use.
    IJBPayDelegate _delegate;
    ```

5.  If the project's current funding cycle is configured to use a data source when receiving payments, ask the data source for the parameters that should be used throughout the rest of the function given provided contextual values. Otherwise default parameters are used. 

    ```solidity
    // If the funding cycle has configured a data source, use it to derive a weight and memo.
    if (fundingCycle.useDataSourceForPay()) {
      (weight, memo, _delegate, _delegateMetadata) = fundingCycle.dataSource().payParams(
        JBPayParamsData(
          _payer,
          _amount,
          fundingCycle.weight,
          fundingCycle.reservedRate(),
          address(uint160(_preferClaimedTokensAndBeneficiary >> 1)),
          _memo,
          _delegateMetadata
        )
      );
      // Otherwise use the funding cycle's weight
    } else {
      weight = fundingCycle.weight;
      memo = _memo;
    }
    ```

6.  Calculate the weighted amount, which is the payment amount multiplied by the appropriate weight. 

    ```solidity
    // Multiply the amount by the weight to determine the amount of tokens to mint.
    uint256 _weightedAmount = PRBMathUD60x18.mul(_amount, weight);
    ```

7.  Increment the project's balance by the amount of the payment received.

    ```solidity
    // Add the amount to the balance of the project.
    balanceOf[_projectId] = balanceOf[_projectId] + _amount;
    ```

    _Internal references:_

    * [`balanceOf`](../properties/balanceof.md)

8. If a there is a weighted amount, mint tokens accordingly. Unpakc the `_preferClaimedTokensAndBeneficiary` value into it's parts to pass along.

    ```solidity
    if (_weightedAmount > 0)
      directory.controllerOf(_projectId).mintTokensOf(
        _projectId,
        _weightedAmount,
        address(uint160(_preferClaimedTokensAndBeneficiary >> 1)),
        'ETH received',
        (_preferClaimedTokensAndBeneficiary & 1) == 0,
        fundingCycle.reservedRate()
      );
    ```

    _External references:_

    * [`mintTokensOf`](../../../or-controllers/jbcontroller/write/minttokensof.md)

9.  Make sure there were at least as many tokens minted as expected. 

    ```solidity
    // The token count must be greater than or equal to the minimum expected.
    require(tokenCount >= _minReturnedTokens, '0x3c: INADEQUATE');
    ```

10. If a pay delegate was provided by the data source, call its `didPay` function with a data payload including contextual information. When finished, emit a `DelegateDidPay` event with the relevant parameters.

    ```solidity
    // If a delegate was returned by the data source, issue a callback to it.
    if (_delegate != IJBPayDelegate(address(0))) {
      JBDidPayData memory _data = JBDidPayData(
        _payer,
        _projectId,
        _amount,
        weight,
        tokenCount,
        payable(address(uint160(_preferClaimedTokensAndBeneficiary >> 1))),
        memo,
        _delegateMetadata
      );
      _delegate.didPay(_data);
      emit DelegateDidPay(_delegate, _data);
    }
    ```

    _Event references:_

    * [`DelegateDidPay`](../events/delegatedidpay.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Records newly contributed ETH to a project.

  @dev
  Mint's the project's tokens according to values provided by a configured data source. If no data source is configured, mints tokens proportional to the amount of the contribution.

  @dev
  Only the associated payment terminal can record a payment.

  @param _payer The original address that sent the payment to the terminal.
  @param _amount The amount that is being paid.
  @param _projectId The ID of the project being paid.
  @param _preferClaimedTokensAndBeneficiary Two properties are included in this packed uint256:
    The first bit contains the flag indicating whether the request prefers to issue tokens claimed as ERC-20s.
    The remaining bits contains the address that should receive benefits from the payment.

    This design is necessary two prevent a "Stack too deep" compiler error that comes up if the variables are declared seperately.
  @param _minReturnedTokens The minimum number of tokens expected to be minted in return.
  @param _memo A memo that will be included in the published event.
  @param _delegateMetadata Bytes to send along to the delegate, if one is used.

  @return fundingCycle The project's funding cycle during which payment was made.
  @return weight The weight according to which new token supply was minted.
  @return tokenCount The number of tokens that were minted.
  @return memo A memo that should be passed along to the emitted event.
*/
function recordPaymentFrom(
  address _payer,
  uint256 _amount,
  uint256 _projectId,
  uint256 _preferClaimedTokensAndBeneficiary,
  uint256 _minReturnedTokens,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  onlyAssociatedPaymentTerminal
  returns (
    JBFundingCycle memory fundingCycle,
    uint256 weight,
    uint256 tokenCount,
    string memory memo
  )
{
  // Get a reference to the current funding cycle for the project.
  fundingCycle = fundingCycleStore.currentOf(_projectId);

  // The project must have a funding cycle configured.
  require(fundingCycle.number > 0, '0x3a: NOT_FOUND');

  // Must not be paused.
  require(!fundingCycle.payPaused(), '0x3b: PAUSED');

  // Save a reference to the delegate to use.
  IJBPayDelegate _delegate;

  // If the funding cycle has configured a data source, use it to derive a weight and memo.
  if (fundingCycle.useDataSourceForPay()) {
    (weight, memo, _delegate, _delegateMetadata) = fundingCycle.dataSource().payParams(
      JBPayParamsData(
        _payer,
        _amount,
        fundingCycle.weight,
        fundingCycle.reservedRate(),
        address(uint160(_preferClaimedTokensAndBeneficiary >> 1)),
        _memo,
        _delegateMetadata
      )
    );
    // Otherwise use the funding cycle's weight
  } else {
    weight = fundingCycle.weight;
    memo = _memo;
  }

  // Multiply the amount by the weight to determine the amount of tokens to mint.
  uint256 _weightedAmount = PRBMathUD60x18.mul(_amount, weight);

  // Add the amount to the balance of the project.
  balanceOf[_projectId] = balanceOf[_projectId] + _amount;

  if (_weightedAmount > 0)
    tokenCount = directory.controllerOf(_projectId).mintTokensOf(
      _projectId,
      _weightedAmount,
      address(uint160(_preferClaimedTokensAndBeneficiary >> 1)),
      'ETH received',
      (_preferClaimedTokensAndBeneficiary & 1) == 0,
      fundingCycle.reservedRate()
    );

  // The token count must be greater than or equal to the minimum expected.
  require(tokenCount >= _minReturnedTokens, '0x3c: INADEQUATE');

  // If a delegate was returned by the data source, issue a callback to it.
  if (_delegate != IJBPayDelegate(address(0))) {
    JBDidPayData memory _data = JBDidPayData(
      _payer,
      _projectId,
      _amount,
      weight,
      tokenCount,
      payable(address(uint160(_preferClaimedTokensAndBeneficiary >> 1))),
      memo,
      _delegateMetadata
    );
    _delegate.didPay(_data);
    emit DelegateDidPay(_delegate, _data);
  }
}
```
{% endtab %}

{% tab title="Errors" %}
| String            | Description                                                         |
| ----------------- | ------------------------------------------------------------------- |
| **`0x3a: NOT_FOUND`** | Thrown if the project doesn't have a funding cycle. |
| **`0x3b: PAUSED`** | Thrown if the project has configured its current funding cycle to pause payments. |
| **`0x3c: INADEQUATE`** | Thrown if the quantity of tokens minted for the beneficiary was less than the minimum expected. |
{% endtab %}

{% tab title="Events" %}
| Name                                        | Data                                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`DelegateDidPay`**](../events/delegatedidpay.md) |  <ul><li><a href="../../../interfaces/ijbpaydelegate.md"><code>IJBPayDelegate</code></a><code>delegate</code></li><li><a href="../../../data-structures/jbdidpaydata.md"><code>JBDidPayData</code></a><code>data</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
