---
description: What's in the V2 protocol
---

# Overview

* **Deploy an NFT that represents ownership over a project**\
  Whichever address owns this NFT has administrative privileges to configure treasury parameters within the Juicebox ecosystem. It can also be used by other Web3 ecosystems to extend functionality to projects.\
  \
  [Learn more about projects](../learn/glossary/project.md)
* **Configure funding cycles for a project**\
  Funding cycles define contractual constraints according to which the project will operate.\
  \
  [Learn more about funding cycles](../learn/glossary/funding-cycle.md)\
  \
  The following properties can be configured into a funding cycle:

<details>

<summary><em>Funding cycle properties</em></summary>

* **Start timestamp**\
  The timestamp at which the funding cycle is considered active. Projects can configure the start time of their first funding cycle to be in the future, and can ensure future reconfigurations don't take effect before a specified timestamp.\
  \
  Once a funding cycle ends, a new one is created automatically that starts right away. If there's an approved queued reconfiguration allowed to start at this time, it will be used, otherwise a copy of the previous funding cycle will be used.

* **Duration**\
  How long each funding cycle lasts, specified in seconds. All funding cycle properties are unchangeable while it is in progress. Any proposed reconfigurations are only able to take effect during a subsequent cycle.\
  \
  If no reconfigurations were submitted by the project owner or if proposed changes fail the current cycle's ballot, a copy of the latest funding cycle will automatically start once the current one ends.\
  \
  A cycle with no duration lasts indefinitely, and proposed changes can take effect right away.

<!---->

* **Distribution limit**\
  The amount of funds that can be distributed from the project's treasury during a funding cycle. The project owner can pre-program a list of destinations to split distributions.\
  \
  Distributing is a public transaction that anyone can call on a project's behalf.

<!---->

* **Overflow allowance**\
  The amount of treasury funds that the project owner can distribute discretionarily on-demand.\
  \
  This allowance does not reset per-funding cycle, it instead lasts until the project owner explicitly proposes a reconfiguration with a new allowance.

<!---->

* **Weight**\
  A number used to determine how many of the project's tokens should be minted and transferred when payments are received during the funding cycle. Project owner's can configure this directly, or allow it to be derived automatically from the previous funding cycle's weight and discount rate. 

<!---->

* **Discount rate**\
  The percent to automatically decrease the subsequent cycle's weight from the current cycle's weight.\
  \
  The discount rate only applies if the project owner doesn't explicitly reconfigure the subsequent cycle's weight to a custom value.\
  \
  [Learn more about discount rates](../learn/glossary/discount-rate.md)

<!---->

* **Ballot**\
  The address of a contract that adheres to [`IJBFundingCycleBallot`](../api/interfaces/ijbfundingcycleballot.md), which can provide custom criteria that prevents a project owner from enacting funding cycle reconfigurations.\
  \
  A simple implementation commonly used by Juicebox projects is to force reconfigurations to be submitted by the project owner at least X days before the end of the current funding cycle, giving the community foresight into any misconfigurations of abuses of power before they take effect.\
  \
  More complex implementation might include on-chain governance.\
  \
  [Learn more ballots](../learn/glossary/ballot.md)

<!---->

* **Reserved rate**\
  The percent of newly minted tokens during the funding cycle that a project wishes to withhold for custom distributions. The project owner can pre-program a list of destinations to split reserved tokens among.\
  \
  [Learn more about reserved rates](../learn/glossary/reserved-tokens.md)

<!---->

* **Redemption rate**\
  The percentage of a project's treasury funds that can be reclaimed by community members by burning the project's tokens during the funding cycle.\
  \
  A rate of 100% suggests a linear proportion, meaning X% of treasury funds can be reclaimed by redeeming X% of the token supply.\
  \
  [Learn more about redemption rates](../learn/glossary/redemption-rate.md)

<!---->

* **Ballot redemption rate**\
  A project can specify a custom redemption rate that takes effect only when a proposed reconfiguration is waiting to take effect.\
  \
  This can be used to automatically allow for more favorable redemption rates during times of potential change.

<!---->

* **Pause payments, pause distributions, pause redemptions, pause mint, pause burn**\
  Projects can pause various bits of its treasury's functionality on a per-funding cycle basis. These functions are unpaused by default.

<!---->

* **Allow changing tokens, allow terminal migrations, allow controller migrations**\
  Projects can allow various bits of treasury functionality on a per-funding cycle basis. These functions are disabled by default.

<!---->

* **Hold fees**\
  Any distributions the project makes from its treasury during a funding cycle configured to hold fees will not pay fees directly to the protocol project's treasury. Instead, the project will have the option to add the distributed funds back into its treasury to unlock the held fees. At any point, the project or JuiceboxDAO can process the held fees, which will channel them through to the protocol project's treasury as usual.\
  \
  This allows a project to withdraw funds and later add them back into their Juicebox treasury without incurring fees.\
  \
  This applies to funds distributions from the distribution limit and from its overflow allowance.

<!---->

* **Data source**\
  The address of a contract that adheres to [`IJBFundingCycleDataSource`](../api/interfaces/ijbfundingcycledatasource.md), which can be used to extend or override what happens when the treasury receives funds, and what happens when someone tries to redeem from the treasury.\
  \
  [Learn more about data sources](../learn/glossary/data-source.md)

</details>

* **Mint tokens**\
  By default, a project starts with 0 tokens and mints them when its treasury receives contributions.\
  A project can mint and distribute more of its own tokens on demand if its current funding cycle isn't configured to pause minting.
* **Burn tokens**\
  Anyone can burn a project's tokens, if the project's current funding cycle isn't configured to paused burning.
* **Bring-your-own token**\
  A project can bring its own token, as long as it adheres to [`IJBToken`](../api/interfaces/ijbtoken.md) and uses fixed point accounting with 18 decimals.\
  \
  This allows a project to use ERC-721's, ERC-1155's, or any other custom contract that'll be called upon when the protocol asks to mint or burn tokens.\
  \
  A project can change its token during any of its funding cycles that are explicitly configured to allow changes.\
  \
  By default, the protocol provides a transaction for projects to deploy [`JBToken`](../api/contracts/jbtoken/) ERC-20 tokens. 
* **Splits**\
  A project can pre-program token distributions to splits. The destination of a split can be an Ethereum address, the project ID of another project's Juicebox treasury (the split will allow you to configure the beneficiary of that project's tokens that get minted in response to the contribution), or to the `allocate(...)` function of any contract that adheres to [`IJBSplitAllocator`](../api/interfaces/ijbsplitallocator.md).\
  \
  [Learn more about splits](../learn/glossary/splits.md)\
  [Learn more about allocators](../learn/glossary/split-allocator.md)
* **Protocol fees**\
  All funds distributed by projects from their treasuries to destinations outside of the juicebox ecosystem will incure a protocol fee. This fee is sent to the JuiceboxDAO treasury which runs on the Juicebox protocol itself (project ID of 1), triggering the same functionality as a payment directly to JuiceboxDAO (by default, minting JBX for the fee payer according to JuiceboxDAO's current funding cycle configuration) from an external source.\
  \
  This fee is adjustable by JuiceboxDAO, with a max value of 5%.\
  \
  Any funds sent from one juicebox treasury to another via splits do not incur fees.
* **Custom treasury strategies**\
  Funding cycles can be configured to use an [`IJBFundingCycleDataSource`](../api/interfaces/ijbfundingcycledatasource.md), [`IJBPayDelegate`](../api/interfaces/ijbpaydelegate.md), and [`IJBRedemptionDelegate`](../api/interfaces/ijbredemptiondelegate.md) to extend or override the default protocol's behavior that defines what happens when an address tries to make a payment to the project's treasury, and what happens when someone tries to redeem the project tokens during any particular funding cycle.\
  \
  [Learn more about data sources](../learn/glossary/data-source.md)\
  [Learn more about delegates](../learn/glossary/delegate.md)
* **Accept multiple tokens**\
  A project can specify any number of payment terminal contracts where it can receive funds denominated in various tokens. This allows projects to create distinct rules for accepting ETH, any ERC-20, or any asset in general.\
  \
  Anyone can roll their own contract that adheres to [`IJBPaymentTerminal`](../api/interfaces/ijbpaymentterminal.md) for projects to use, and a project can migrate funds between terminals that use the same token as it wishes.
* **Forkability and migratability.**\
  A project can migrate its treasury's controller to any other contract that adheres to [`IJBController`](../api/interfaces/ijbcontroller.md). This allows a project to evolve into updated or custom treasury dynamics rules over time as it wishes.
* **Operators**\
  A project owner can specify addresses that are allowed to operate certain administrative treasury transactions on its behalf.\
  \
  [Learn more about operators](../learn/glossary/operator.md)
