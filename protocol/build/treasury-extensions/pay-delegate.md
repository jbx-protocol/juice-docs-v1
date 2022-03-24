# Pay delegate

Before implementing, learn about delegates [here](../../learn/glossary/delegate.md).
### Specs

A contract can become a treasury pay delegate by adhering to [`IJBPayDelegate`](../../api/interfaces/ijbpaydelegate.md):

```solidity
interface IJBPayDelegate {
  function didPay(JBDidPayData calldata _param) external;
}
```

When extending the pay functionality with a delegate, the protocol will pass a [`JBDidPayData`](../../api/data-structures/jbdidpaydata.md) to the `didPay(...)` function:

```solidity
struct JBDidPayData {
  // The address from which the payment originated.
  address payer;
  // The ID of the project for which the payment was made.
  uint256 projectId;
  // The amount of the payment. Includes the token being paid, the value, the number of decimals included, and the currency of the amount.
  JBTokenAmount amount;
  // The number of project tokens minted for the beneficiary.
  uint256 projectTokenCount;
  // The address to which the tokens were minted.
  address beneficiary;
  // The memo that is being emitted alongside the payment.
  string memo;
  // Metadata to send to the delegate.
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
  // The expected currency of the value.
  uint256 currency;
}
```

The `msg.sender` to the delegate will be the payment terminal that facilitated the payment. 

In payment terminals based on the [`JBPayoutRedemptionPaymentTerminal`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal), such as [`JBETHPaymentTerminal`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/)'s and [`JBERC20PaymentTerminal`](../../api/contracts/or-payment-terminals/jberc20paymentterminal/)'s, the pay delegate hook gets called _after_ the project's tokens have been minted and distributed. [View the docs](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/_pay.md). 

Make sure to only allow trusted contracts to access the `didPay(...)` transaction.

### Attaching

A delegate contract should be deployed independently. Once deployed, it's address can be returned from a data source hook. See [how to build a data source](./data-source.md) for more.