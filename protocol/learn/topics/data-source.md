# Data source

#### What everyone needs to know

* A data source contract is a way of providing extensions to your juicebox treasury that either overrides or augments the default `JBETHPaymentTerminal` data sources.
* A data source contract can be used to provide custom data to the `pay` transaction and/or the `redeem` transaction.
* A data source is responsible for specifying any `delegate` hooks that should be triggered after the core functionality of a `pay` or `redeem` transaction executes successfully. 
* Each `IJBTerminal` fork can leverage data sources in unique ways.

#### What you'll want to know if you're building

* _wip_
