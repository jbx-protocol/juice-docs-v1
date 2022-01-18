# Delegate

#### What everyone needs to know

* A delegate contract is a way of providing extensions to your juicebox treasury that either overrides or augments the default `JBETHPaymentTerminal` behavior.
* There are two types of delegates: `IJBPayDelegate`s and `IJBRedemptionDelegate`s. Any contract that adheres to these interfaces can be used as a delegate in your project's funding cycles.
* Pay delegates include a custom `didPay` hook that will execute after all of the default Juicebox pay logic has successfully executed in the terminal contract. The hook is passed a bunch of contextual information via a `JBDidPayData` struct.
* Redemption delegates include a custom `didRedeem` hook that will execute after all of the default Juicebox redeem logic has successfully executed in the terminal contract. The hook is passed a bunch of contextual information via a `JBDidRedeemData` struct. The `didReem` hook gets called before any reclaimed ETH is transfered out of the terminal contract.
* Each `IJBTerminal` fork can leverage delegates in unique ways.

#### What you'll want to know if you're building

* _wip_
