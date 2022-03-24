# Delegate

#### What everyone needs to know

* A delegate contract is a way of providing extensions to a treasury that either overrides or augments the default [`JBPayoutRedemptionPaymentTerminal`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/) behavior.
* There are two types of delegates: [`IJBPayDelegate`](../../api/interfaces/ijbpaydelegate.md)s and [`IJBRedemptionDelegate`](../../api/interfaces/ijbredemptiondelegate.md)s. Any contract that adheres to these interfaces can be used as a delegate in a project's funding cycles.
* Pay delegates include a custom `didPay(...)` hook that will execute after all of the default protocol pay logic has successfully executed in the terminal contract. The hook is passed a bunch of contextual information via a [`JBDidPayData`](../../api/data-structures/jbdidpaydata.md) struct.
* Redemption delegates include a custom `didRedeem(...)` hook that will execute after all of the default protocol redeem logic has successfully executed in the terminal contract. The hook is passed a bunch of contextual information via a [`JBDidRedeemData`](../../api/data-structures/jbdidredeemdata.md) struct. The `didRedeem(...)` hook gets called before any reclaimed tokens are transfered out of the terminal contract.
* Each [`IJBPaymentTerminal`](../../api/interfaces/ijbpaymentterminal.md) fork can leverage delegates in unique ways.

#### What you'll want to know if you're building

* A delegate contract can be specified by the funding cycle's [`dataSource`](data-source.md).
* The [`IJBPayDelegate`](../../api/interfaces/ijbpaydelegate.md)'s `didPay(...)` hook is triggered in [`JBPayoutRedemptionPaymentTerminal._pay(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/_pay.md), and the [`IJBRedemptionDelegate`](../../api/interfaces/ijbredemptiondelegate.md)'s `didRedeem(...)` hook is triggered in [`JBPayoutRedemptionPaymentTerminal.redeemTokensOf(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/redeemtokensof.md).
* The redemption delegate hook is called before funds are dispersed.
* [Get started building pay delegates](../../build/treasury-extensions/pay-delegate.md).
* [Get started building redemption delegates](../../build/treasury-extensions/redemption-delegate.md).
