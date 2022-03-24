# Redemption delegate

Before implementing, learn about delegates [here](../../learn/glossary/delegate.md).
### Specs

A contract can become a treasury redemption delegate by adhering to [`IJBRedemptionDelegate`](../../api/interfaces/ijbredemptiondelegate.md):

```solidity
interface IJBRedemptionDelegate {
  function didRedeem(JBDidRedeemData calldata _param) external;
}
```

When extending the redemption functionality with a delegate, the protocol will pass a [`JBDidRedeemData`](../../api/data-structures/jbdidredeemdata.md) to the `didRedeem(...)` function:

```solidity
struct JBDidRedeemData {
  // The holder of the tokens being redeemed.
  address holder;
  // The project to which the redeemed tokens are associated.
  uint256 projectId;
  // The number of project tokens being redeemed.
  uint256 projectTokenCount;
  // The reclaimed amount. Includes the token being paid, the value, the number of decimals included, and the currency of the amount.
  JBTokenAmount reclaimedAmount;
  // The address to which the reclaimed amount will be sent.
  address payable beneficiary;
  // The memo that is being emitted alongside the redemption.
  string memo;
  // Metadata to send to the delegate.
  bytes metadata;
}
```

```solidity
struct JBTokenAmount {
  // The token the reclaimed amount will be made in.
  address token;
  // The amount of tokens that were reclaimed, as a fixed point number.
  uint256 value;
  // The number of decimals included in the value fixed point number.
  uint256 decimals;
  // The expected currency of the value.
  uint256 currency;
}
```

The `msg.sender` to the delegate will be the payment terminal that facilitated the redemption. 

In payment terminals based on the [`JBPayoutRedemptionPaymentTerminal`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal), such as [`JBETHPaymentTerminal`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/)'s and [`JBERC20PaymentTerminal`](../../api/contracts/or-payment-terminals/jberc20paymentterminal/)'s, the redemption delegate hook gets called _before_ the reclaimed amount is sent to the redemption beneficiary, but after all internal accounting has been updated.  [View the docs](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/redeemtokensof.md). 

Make sure to only allow trusted contracts to access the `didPay(...)` transaction.

### Attaching

A delegate contract should be deployed independently. Once deployed, it's address can be returned from a data source hook. See [how to build a data source](./data-source.md) for more.