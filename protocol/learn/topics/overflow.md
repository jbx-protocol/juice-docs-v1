# Splits

#### What everyone needs to know

* A project can set distribution limits from its treasury on a per funding cycle basis within the [`JbController.launchProjectFor(...)`] and [`reconfigureFundingCyclesOf(...)`] transactions. These limits are stored in [`JBController.distributionLimitsOf(...)`](). Any funds that are in the project's juicebox treasury that the it hasn't specified as distributable is considered overflow.
* Overflow serves as a projects runway since a project's future funding cycles can tap into it within the bounds of the preconfigured distribution limits. 
* By default, overflow also serves a means for allowing community memebers to exit with a portion of the treasury's funds in hand. Any funds in overflow are claimable by the project's community by redeeming/burning community tokens. Projects can override or extend this functionality.
* Projects can manage how much money is in overflow, and therefor how much each member can exit with, by either adjusting its distribution limits or by holding treasury funds outside of Juicebox, such as in a multisig wallet.
* A project can set overflow allowances from its treasury on a per funding cycle configuration basis within the [`JbController.launchProjectFor(...)`] and [`reconfigureFundingCyclesOf(...)`] transactions. These allowances are stored in [`JBController.overflowAllowancesOf(...)`](). A project's owner can distribute the project's funds from its overflow on-demand up until the preconfigured allowance. Overflow allowances do not reset each funding cycle, they last until a funding cycle reconfiguration is made. 

#### What you'll want to know if you're building

_wip_