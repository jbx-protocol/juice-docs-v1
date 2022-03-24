# Learn | Redemption rate

#### What everyone needs to know

* A redemption rate determines the proportion of treasury assets that can be acquired by a token holder by burning their tokens.
* By default, all treasury assets that are considered overflow can be redeemed for by token holders. This can be modified using [data source](data-source.md) extensions.
* A project's redemption rate and extensions can be reconfigured each funding cycle.
* A redemption rate of 100% is linear, meaning a holder of 1% of the token supply can be redeemed for 1% of available treasury assets.
* A redemption rate of `x`% where `x` < 100% will leave some proportional assets on the table to share between those who wait longer to redeem. The smaller the `x`, the less assets `x` can be redeemed for.

#### What you'll want to know if you're building

* A redemption rate can be specified in a funding cycle through the [`JBController.launchProjectFor(...)`](../../api/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) or [`JBController.reconfigureFundingCyclesOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md) transactions.
* A ballot redemption rate can be specified in a funding cycle through the [`JBController.launchProjectFor(...)`](../../api/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) or [`JBController.reconfigureFundingCyclesOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md) transactions, which will override the standard redemption rate if there is currently a [reconfiguration ballot active](ballot.md).
