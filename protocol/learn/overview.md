---
description: What's in the V2 protocol
---

# Overview

* <mark style="color:orange;">**Deploy an NFT that represents ownership over your project**</mark>\
  Whichever address owns this NFT has administrative privileges to configure treasury parameters within the Juicebox ecosystem. It can also be used by other Web3 ecosystems to extend functionality to projects.
* <mark style="color:orange;">**Configure funding cycles for your project**</mark>\
  Funding cycles define contractual constraints according to which your project will operate.\
  The following properties can be configured into a funding cycle:

<details>

<summary><em>Funding cycle properties</em></summary>

* <mark style="color:orange;">**Duration**</mark>\
  How long each funding cycle lasts, specified in seconds. All funding cycle properties are unchangeable while it is in progress. Any proposed reconfigurations are only able to take effect during a subsequent cycle.\
  \
  If no reconfigurations were submitted by the project owner or if proposed changes fail the current cycle's ballot, a copy of the latest funding cycle will automatically start once the current one ends.\
  \
  A cycle with no duration lasts indefinitely, and proposed changes can take effect right away.

<!---->

* <mark style="color:orange;">**Distribution limit**</mark>\
  The amount of funds that can be distributed from the project's treasury during a funding cycle. The project owner can pre-program a list of destinations to split distributions.\
  \
  Distributing is a public transaction that anyone can call on a project's behalf.

<!---->

* <mark style="color:orange;">**Overflow allowance**</mark>\
  The amount of treasury funds that the project owner can distribute discretionarily on-demand.\
  \
  This allowance does not reset per-funding cycle, it instead lasts until the project owner explicitly proposes a reconfiguration with a new allowance.

<!---->

* <mark style="color:orange;">**Weight**</mark>\
  A number used to determine how many of the project's tokens should be minted and transferred when payments are received during the funding cycle.

<!---->

* <mark style="color:orange;">**Discount rate**</mark>\
  The percent to automatically decrease the subsequent cycle's weight from the current cycle's weight.\
  \
  The discount rate only applies if the project owner doesn't explicitly reconfigure the subsequent cycle's weight to a custom value.

<!---->

* <mark style="color:orange;">**Ballot**</mark>\
  The address of a contract that adheres to [`IJBFundingCycleBallot`](../specifications/interfaces/ijbfundingcycleballot.md), which can provide custom criteria that prevents a project owner from enacting funding cycle reconfigurations.\
  \
  A simple implementation commonly used by Juicebox projects is to force reconfigurations to be submitted by the project owner at least X days before the end of the current funding cycle, giving the community foresight into any misconfigurations of abuses of power before they take effect.\
  \
  More complex implementation might include on-chain governance.

<!---->

* <mark style="color:orange;">**Reserved rate**</mark>\
  The percent of newly minted tokens during the funding cycle that a project wishes to withhold for custom distributions. The project owner can pre-program a list of destinations to split reserved tokens.

<!---->

* <mark style="color:orange;">**Redemption rate**</mark>\
  The percentage of a project's treasury funds that can be reclaimed by community members by burning the project's tokens during the funding cycle.\
  \
  A rate of 100% suggests a linear proportion, meaning X% of treasury funds can be reclaimed by redeeming X% of the token supply.

<!---->

* <mark style="color:orange;">**Ballot redemption rate**</mark>\
  A project can specify a custom redemption rate that takes effect only when a proposed reconfiguration is waiting to take effect.\
  \
  This can be used to automatically allow for more favorable redemption rates during times of potential change.

<!---->

* <mark style="color:orange;">**Pause payments, pause distributions, pause redemptions, pause mint, pause burn**</mark>\
  Projects can pause various bits of its treasury's functionality on a per-funding cycle basis. These functions are unpaused by default.

<!---->

* <mark style="color:orange;">**Allow changing tokens, allow terminal migrations, allow controller migrations**</mark>\
  Projects can allow various bits of treasury functionality on a per-funding cycle basis. These functions are disabled by default.

<!---->

* <mark style="color:orange;">**Hold fees**</mark>\
  Any distributions the project makes from its treasury during a funding cycle configured to hold fees will not pay fees directly to the protocol project's treasury. Instead, the project will have the option to add the distributed funds back into its treasury to unlock the held fees. At any point, the project or JuiceboxDAO can process the held fees, which will channel them through to the protocol project's treasury as usual.\
  \
  This allows a project to withdraw funds and later add them back into their Juicebox treasury without incurring fees.\
  \
  This applies to funds distributions from the distribution limit and from its overflow allowance.

<!---->

* <mark style="color:orange;">**Data source**</mark>\
  The address of a contract that adheres to [`IJBFundingCycleDataSource`](../specifications/interfaces/ijbfundingcycledatasource.md), which can be used to extend or override what happens when your treasury is receiving funds, and what happens when someone tries to redeem from your treasury.

</details>

* <mark style="color:orange;">**Mint tokens**</mark>\
  By default, a project starts with 0 tokens and mints them when its treasury receives contributions.\
  A project can mint and distribute more of its own tokens on demand if it its current funding cycle isn't configured to pause minting.
* <mark style="color:orange;">**Burn tokens**</mark>\
  Anyone can burn a project's tokens, if the project's current funding cycle isn't configured to paused burning.
* <mark style="color:orange;">**Bring-your-own token**</mark>\
  A project can bring its own token, as long as it adheres to `IJBToken`.\
  \
  This allows a project to use ERC-721's, ERC-1155's, or any other custom contract that'll be called upon when the protocol asks to mint or burn tokens.\
  \
  A project can change its token during any of its funding cycles that are explicitly configured to allow it.
* <mark style="color:orange;">**Splits**</mark>\
  A project can pre-program token distributions to splits. The destination of a split can be an Ethereum address, the project ID of another project's Juicebox treasury (the split will allow you to configure the beneficiary of that project's tokens that get minted in response to the distribution), or to the `allocate` function of any contract that adheres to [`IJBSplitAllocator`](../specifications/interfaces/ijbsplitallocator.md).\
  \
  ETH splits to Allocators get sent directly to the `allocate` function. Distribution of other assets to Allocator contracts (ERC-20's, ERC-721's, ERC-1155's, etc) will trigger the `allocate` function after a successful transfer.
* <mark style="color:orange;">**Custom treasury strategies**</mark>\
  Funding cycles can be configured to use an [`IJBFundingCycleDataSource`](../specifications/interfaces/ijbfundingcycledatasource.md), [`IJBPayDelegate`](../specifications/interfaces/ijbpaydelegate.md), and [`IJBRedemptionDelegate`](../specifications/interfaces/ijbredemptiondelegate.md) to extend or override the default Juicebox protocol's behavior that defines what happens when an address tries to make a payment to your project's treasury, and what happens when someone tries to redeem your tokens during any particular funding cycle.
* <mark style="color:orange;">**Accept multiple tokens**</mark>\
  A project can specify any number of payment terminal contracts where it can receive funds denominated in various tokens. This allows projects to create distinct rules for accepting ETH, any ERC-20, or any asset in general.\
  \
  Anyone can roll their own contract that adheres to `IJBTerminal` for projects to use, and a project can migrate funds between terminals that use the same token as it wishes.
* <mark style="color:orange;">**Forkability and migratability.**</mark>\
  A project can migrate its treasury's controller to any other contract that adheres to `IJBController`. This allows a project to evolve into updated or custom operating rules over time as it wishes.
* <mark style="color:orange;">**Operators**</mark>\
  A project owner can specify addresses that are allowed to operate certain administrative treasury transactions on its behalf.
