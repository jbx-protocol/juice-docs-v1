# Data source

#### What everyone needs to know

* A data source must adhere to the [`IJBFundingCycleDataSource`](../../api/interfaces/ijbfundingcycledatasource.md) interaface.
* A data source contract is a way of providing extensions to your Juicebox treasury that either overrides or augments the default [`JBETHPaymentTerminal`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/) data sources.
* A data source contract can be used to provide custom data to the [`pay`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/write/pay.md) transaction and/or the [`redeemTokensOf`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/write/redeemtokensof.md) transaction.
* A data source is passed contextual information from the transactions, from which it can derive custom data for the protocol to use to affect subsequent behaviors in the pay and redeem transactions. Contextual information from the pay transaction is passed to the data source in the form of [`JBPayParamsData`](../../api/data-structures/jbpayparamsdata.md) , and contextual information from the redeem transaction is passed to the data source in the form of [`JBRedeemParamsData`](../../api/data-structures/jbredeemparamsdata.md).
* A data source is responsible for specifying any [delegate](delegate.md) hooks that should be triggered after the core functionality of a [`pay`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/write/pay.md) or [`redeemTokensOf`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/write/redeemtokensof.md) transaction executes successfully.
* Each [`IJBPaymentTerminal`](../../api/interfaces/ijbpaymentterminal.md) fork can leverage data sources in unique ways.

#### What you'll want to know if you're building

* A data source contract can be specified in a funding cycle, along with flags that indicate if the funding cycle should `useDataSourceForPay` and/or `useDataSourceForRedeem`. These are set either in [`JBController.launchProjectFor(...)`](../../api/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) or [`JBController.reconfigureFundingCyclesOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md).
* A funding cycle's data source is called upon in [`JBETHPaymentTerminalStore.recordPaymentFrom(...)`](../../api/contracts/or-payment-terminals/jbethpaymentterminalstore/write/recordpaymentfrom.md) and in [`JBETHPaymentTerminalStore.recordRedemptionFor(...)`](../../api/contracts/or-payment-terminals/jbethpaymentterminalstore/write/recordredemptionfor.md).
* If a data source is not specified in a funding cycle, or if flags aren't explicitly set, default Juicebox data will be used which closely mirrors Juicebox V1 dynamics.
