# Delegate

#### What everyone needs to know

* A delegate contract is a way of providing extensions to your juicebox treasury that either overrides or augments the default [`JBETHPaymentTerminal`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/) behavior.
* There are two types of delegates: [`IJBPayDelegate`](../../api/interfaces/ijbpaydelegate.md)s and [`IJBRedemptionDelegate`](../../api/interfaces/ijbredemptiondelegate.md)s. Any contract that adheres to these interfaces can be used as a delegate in your project's funding cycles.
* Pay delegates include a custom `didPay` hook that will execute after all of the default Juicebox pay logic has successfully executed in the terminal contract. The hook is passed a bunch of contextual information via a [`JBDidPayData`](../../api/data-structures/jbdidpaydata.md) struct.
* Redemption delegates include a custom `didRedeem` hook that will execute after all of the default Juicebox redeem logic has successfully executed in the terminal contract. The hook is passed a bunch of contextual information via a [`JBDidRedeemData`](../../api/data-structures/jbdidredeemdata.md) struct. The `didRedeem` hook gets called before any reclaimed ETH is transferred out of the terminal contract.
* Each [`IJBPaymentTerminal`](../../api/interfaces/ijbpaymentterminal.md) fork can leverage delegates in unique ways.

#### What you'll want to know if you're building

* A delegate contract can be specified by your funding cycle's [`dataSource`](data-source.md).
* The [`IJBPayDelegate`](../../api/interfaces/ijbpaydelegate.md)'s `didPay` hook is triggered in [`JBPaymentTerminalStore.recordPaymentFrom(...)`](../../api/contracts/jbpaymentterminalstore/write/recordpaymentfrom.md), and the [`IJBRedemptionDelegate`](../../api/interfaces/ijbredemptiondelegate.md)'s `didRedeem` hook is triggered in [`JBPaymentTerminalStore.recordRedemptionFor(...)`](../../api/contracts/jbpaymentterminalstore/write/recordredemptionfor.md).
* The redemption delegate hook is called before funds are dispersed.&#x20;
