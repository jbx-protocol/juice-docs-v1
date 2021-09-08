# Table of contents

* [Initial page](README.md)

## Getting Started

* [Create a Juicebox Project](getting-started/create-a-juicebox-project.md)

## Resources

* [FAQ](resources/faq.md)

## Protocol

* [Overview](protocol/overview.md)
* [Terminology](protocol/terminology.md)
* [Contracts](protocol/contracts/README.md)
  * [TerminalV2PaymentLayer](protocol/contracts/terminalv2paymentlayer/README.md)
    * [Events](protocol/contracts/terminalv2paymentlayer/events.md)
    * [Read](protocol/contracts/terminalv2paymentlayer/read.md)
    * [Write](protocol/contracts/terminalv2paymentlayer/write/README.md)
      * [Pay](protocol/contracts/terminalv2paymentlayer/write/pay.md)
  * [TerminalV2DataLayer](protocol/contracts/terminalv2datalayer/README.md)
    * [Events](protocol/contracts/terminalv2datalayer/events.md)
    * [Read](protocol/contracts/terminalv2datalayer/read/README.md)
      * [projects](protocol/contracts/terminalv2datalayer/read/projects.md)
      * [fundingCycles](protocol/contracts/terminalv2datalayer/read/fundingcycles.md)
      * [ticketBooth](protocol/contracts/terminalv2datalayer/read/ticketbooth.md)
      * [splitsStore](protocol/contracts/terminalv2datalayer/read/splitsstore.md)
      * [prices](protocol/contracts/terminalv2datalayer/read/prices.md)
      * [terminalDirectory](protocol/contracts/terminalv2datalayer/read/terminaldirectory.md)
      * [balanceOf](protocol/contracts/terminalv2datalayer/read/balanceof.md)
      * [migrationIsAllowed](protocol/contracts/terminalv2datalayer/read/migrationisallowed.md)
      * [remainingOverflowAllowanceOf](protocol/contracts/terminalv2datalayer/read/remainingoverflowallowanceof.md)
      * [paymentLayer](protocol/contracts/terminalv2datalayer/read/paymentlayer.md)
      * [fee](protocol/contracts/terminalv2datalayer/read/fee.md)
      * [currentOverflowOf](protocol/contracts/terminalv2datalayer/read/currentoverflowof.md)
      * [reservedTokenBalanceOf](protocol/contracts/terminalv2datalayer/read/reservedtokenbalanceof.md)
      * [claimableOverflowOf](protocol/contracts/terminalv2datalayer/read/claimableoverflowof.md)
    * [Write](protocol/contracts/terminalv2datalayer/write/README.md)
      * [reconfigureFundingCycles](protocol/contracts/terminalv2datalayer/write/reconfigurefundingcycles.md)
      * [launchProject](protocol/contracts/terminalv2datalayer/write/launchproject.md)
      * [mintTokens](protocol/contracts/terminalv2datalayer/write/minttokens.md)
      * [burnTokens](protocol/contracts/terminalv2datalayer/write/burntokens.md)
      * [distributeReservedTokens](protocol/contracts/terminalv2datalayer/write/distributereservedtokens.md)
      * [prepForMigrationOf](protocol/contracts/terminalv2datalayer/write/prepformigrationof.md)
      * [recordPayment](protocol/contracts/terminalv2datalayer/write/recordpayment.md)
      * [recordWithdrawal](protocol/contracts/terminalv2datalayer/write/recordwithdrawal.md)
      * [recordUsedAllowance](protocol/contracts/terminalv2datalayer/write/recordusedallowance.md)
      * [recordRedemption](protocol/contracts/terminalv2datalayer/write/recordredemption.md)
      * [recordMigration](protocol/contracts/terminalv2datalayer/write/recordmigration.md)
      * [recordAddedBalance](protocol/contracts/terminalv2datalayer/write/recordaddedbalance.md)
      * [allowMigration](protocol/contracts/terminalv2datalayer/write/allowmigration.md)
      * [setPaymentLayer](protocol/contracts/terminalv2datalayer/write/setpaymentlayer.md)
      * [pay](protocol/contracts/terminalv2datalayer/write/pay.md)
      * [addToBalance](protocol/contracts/terminalv2datalayer/write/addtobalance.md)
  * [TerminalDirectory](protocol/contracts/terminaldirectory/README.md)
    * [Events](protocol/contracts/terminaldirectory/events.md)
    * [Read](protocol/contracts/terminaldirectory/read.md)
    * [Write](protocol/contracts/terminaldirectory/write.md)
  * [Projects](protocol/contracts/projects.md)
  * [TicketBooth](protocol/contracts/ticketbooth/README.md)
    * [Events](protocol/contracts/ticketbooth/events.md)
    * [Read](protocol/contracts/ticketbooth/read.md)
    * [Write](protocol/contracts/ticketbooth/write.md)
  * [FundingCycles](protocol/contracts/fundingcycles/README.md)
    * [Events](protocol/contracts/fundingcycles/events.md)
    * [Read](protocol/contracts/fundingcycles/read.md)
    * [Write](protocol/contracts/fundingcycles/write.md)
  * [SplitStore](protocol/contracts/splitstore/README.md)
    * [Events](protocol/contracts/splitstore/events.md)
    * [Read](protocol/contracts/splitstore/read.md)
    * [Write](protocol/contracts/splitstore/write.md)
  * [OperatorStore](protocol/contracts/operatorstore/README.md)
    * [Events](protocol/contracts/operatorstore/events.md)
    * [Read](protocol/contracts/operatorstore/read.md)
    * [Write](protocol/contracts/operatorstore/write.md)
  * [Prices](protocol/contracts/prices/README.md)
    * [Events](protocol/contracts/prices/events.md)
    * [Read](protocol/contracts/prices/read.md)
    * [Write](protocol/contracts/prices/write.md)

## Contracts

* [TicketBooth](contracts/ticketbooth/README.md)
  * [Write](contracts/ticketbooth/write.md)
  * [Read](contracts/ticketbooth/read.md)
  * [DirectPaymentAddress](contracts/ticketbooth/directpaymentaddress.md)
  * [Funding Cycles](contracts/ticketbooth/funding-cycles.md)
  * [Governance](contracts/ticketbooth/governance.md)
  * [ModStore](contracts/ticketbooth/modstore.md)
  * [OperatorStore](contracts/ticketbooth/operatorstore.md)
  * [Prices](contracts/ticketbooth/prices.md)
  * [Projects](contracts/ticketbooth/projects.md)
  * [Tickets](contracts/ticketbooth/tickets.md)
  * [TerminalV1](contracts/ticketbooth/terminalv1.md)
* [TerminalDirectory](contracts/terminal-directory.md)
* [Mods](contracts/mods.md)
* [TerminalV1](contracts/terminalv1/README.md)
  * [Read](contracts/terminalv1/read.md)
  * [Write](contracts/terminalv1/write.md)

## Developers

* [Integration Guide](developers/integration-guide.md)
* [Subgraph](developers/subgraph.md)

## Hiring

* [Join the team](hiring/join-the-team/README.md)
  * [Discord Manager & Moderator](hiring/join-the-team/discord-manager-and-moderator.md)

## Links

* [Juicebox.money](https://juicebox.money)
* [Github](https://github.com/jbx-protocol)
* [Twitter](https://twitter.com/juiceboxETH)
* [Discord](https://discord.gg/5JsDvuyCPd)

