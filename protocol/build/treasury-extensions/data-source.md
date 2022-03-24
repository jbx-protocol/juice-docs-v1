# Data source

## Specs

A contract can become a treasury data source by adhering to [`IJBFundingCycleDataSource`](../../api/interfaces/ijbfundingcycledatasource.md):

```solidity
interface IJBFundingCycleDataSource {
  function payParams(JBPayParamsData calldata _param)
    external
    view
    returns (
      uint256 weight,
      string memory memo,
      IJBPayDelegate delegate
    );

  function redeemParams(JBRedeemParamsData calldata _param)
    external
    view
    returns (
      uint256 reclaimAmount,
      string memory memo,
      IJBRedemptionDelegate delegate
    );
}
```

There are two functions that must be implemented, `payParams(...)` and `redeemParams(...)`. Either one can be left empty if the intent is to only extend the treasury's pay functionality or redeem functionality.

#### Pay

When extending the pay functionality, the protocol will pass a [`JBPayParamsData`](../../api/data-structures/jbpayparamsdata.md) to the `payParams(...)` function:

```solidity
struct JBPayParamsData {
  // The terminal that is facilitating the payment.
  IJBPaymentTerminal terminal;
  // The address from which the payment originated.
  address payer;
  // The amount of the payment. Includes the token being paid, the value, the number of decimals included, and the currency of the amount.
  JBTokenAmount amount;
  // The ID of the project being paid.
  uint256 projectId;
  // The weight of the funding cycle during which the payment is being made.
  uint256 weight;
  // The reserved rate of the funding cycle during which the payment is being made.
  uint256 reservedRate;
  // The memo that was sent alongside the payment.
  string memo;
  // Arbitrary metadata provided by the payer.
  bytes metadata;
}
```

```solidity
struct JBTokenAmount {
  // The token the payment was made in.
  address token;
  // The amount of tokens that was paid, as a fixed point number.
  uint256 value;
  // The number of decimals included in the value fixed point number.
  uint256 decimals;
  // The expected currency index of the value.
  uint256 currency;
}
```

Using these params, the data source's `payParams(...)` function is responsible for returning a few bits of information:

* `weight` is a fixed point number with 18 decimals that the protocol can use to base arbitrary calculations on. For example, payment terminals based on the [`JBPayoutRedemptionPaymentTerminal`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal), such as [`JBETHPaymentTerminal`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/)'s and [`JBERC20PaymentTerminal`](../../api/contracts/or-payment-terminals/jberc20paymentterminal/)'s, use the `weight` to determine how many project tokens to mint when a project receives a payment (see [the calculation](../../api/contracts/jbpaymentterminalstore/write/recordpaymentfrom.md)). By default, the protocol will use the `weight` of the project's current funding cycle, which is provided to the data source function in `JBPayParamsData.weight`. Increasing the weight will mint more tokens and decreasing the weight will mint fewer tokens, both as a function of the amount paid. Return the `JBPayParamsData.weight` value if no altered functionality is desired.
* `memo` is a string that will get emitted within the [`Pay`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/events/pay.md) event and sent along to any `delegate` that this function also returns. By default, the protocol will use the `memo` passed in directly by the payer, which is provided to this data source function in `JBPayParamsData.memo`. Return the `JBPayParamsData.memo` value if no altered functionality is desired.
* `delegate` is the address of a contract that adheres to [`IJBPayDelegate`](../../api/interfaces/ijbpaydelegate.md) whose `didPay(...)` function will be called once the protocol finishes its standard payment routine. Checkout how to [build a pay delegate](pay-delegate.md) for more details. If the same contract is being used as the data source and the pay delegate, return `address(this)`. Return the zero address if no additional functionality is desired.

The `payParams(...)` function can also revert if it's presented with any conditions it does not want to accept payments under. 

#### Redeem
 
When extending the redeem functionality, the protocol will pass a [`JBRedeemParamsData`](../../api/data-structures/jbredeemparamsdata.md) to the `redeemParams(...)` function:

```solidity
struct JBRedeemParamsData {
  // The terminal that is facilitating the redemption.
  IJBPaymentTerminal terminal;
  // The holder of the tokens being redeemed.
  address holder;
  // The ID of the project whos tokens are being redeemed.
  uint256 projectId;
  // The proposed number of tokens being redeemed, as a fixed point number with 18 decimals.
  uint256 tokenCount;
  // The number of decimals included in the reclaim amount fixed point number.
  uint256 decimals;
  // The currency that the reclaim amount is expected to be in terms of.
  uint256 currency;
  // The amount that should be reclaimed by the redeemer using the protocol's standard bonding curve redemption formula.
  uint256 reclaimAmount;
  // If overflow across all of a project's terminals is being used when making redemptions.
  bool useTotalOverflow;
  // The redemption rate of the funding cycle during which the redemption is being made.
  uint256 redemptionRate;
  // The ballot redemption rate of the funding cycle during which the redemption is being made.
  uint256 ballotRedemptionRate;
  // The proposed memo that is being emitted alongside the redemption.
  string memo;
  // Arbitrary metadata provided by the redeemer.
  bytes metadata;
}
```

Using these params, the data source's `redeemParams(...)` function is responsible for returning a few bits of information:

* `reclaimAmount` is the amount of tokens the terminal should send out to the redemption beneficiary as a result of burning the amount of tokens specified in `JBRedeemParamsData.tokenCount`, as a fixed point number with the same amount of decimals as `JBRedeemParamsData.decimals`. By default, the protocol will use a recliam amount determined by the standard protocol bonding curve based on the redemption rate the project has configured into its current funding cycle, which is provided to the data source function in `JBRedeemParamsData.reclaimAmount`. Return the `JBRedeemParamsData.reclaimAmount` value if no altered functionality is desired.
* `memo` is a string that will get emitted within the [`RedeemTokens`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/events/redeemtokens.md) event and sent along to any `delegate` that this function also returns. By default, the protocol will use the `memo` passed in directly by the redeemer, which is provided to this data source function in `JBRedeemParamsData.memo`. Return the `JBRedeemParamsData.memo` value if no altered functionality is desired.
* `delegate` is the address of a contract that adheres to [`IJBRedemptionDelegate`](../../api/interfaces/ijbredemptiondelegate.md) whose `didRedeem(...)` function will be called once the protocol finishes its standard redemption routine (but before the reclaimed amount is sent to the beneficiary). Checkout how to [build a redemption delegate](redemption-delegate.md) for more details. If the same contract is being used as the data source and the redemption delegate, return `address(this)`. Return the zero address if no additional functionality is desired.

The `redeemParams(...)` function can also revert if it's presented with any conditions it does not want to accept redemptions under. 

## Attaching

A data source contract should be deployed independently. Once deployed, it's address can be configured into a project's funding cycle metadata to take effect while that funding cycle is active. Additionally, the metadata's `useDataSourceForPay` and/or `useDataSourceForRedeem` should be set to `true` if the respective data source hook should be referenced by the protocol.