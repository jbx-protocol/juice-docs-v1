# README

Welcome all curious developers to the Juicebox V2 protocol docs.

The Juicebox protocol is a programmable treasury. Projects can use it to configure how its tokens should be minted when it receives money, and under what conditions funds can be distributed to preprogrammed addresses or claimed by its community. These rules can evolve over funding cycles, allowing people to bootstrap open-ended projects and add structure, constraints, and incentives over time as needed. The protocol is light enough for a group of friends, yet powerful enough for a global network of anons sharing thousands of ETH.

The protocol is nuanced, however. The goal of this section is for you to find any protocol related information that you're looking for. Whether you're auditing, developing your own JB project, creating your own JB extensions, looking for bug bounties, or just looking to learn some solidity, these docs should give you just what you're looking for. These docs should allow you to click around and get a real good deep dive, and should just as easily allow you to find overview information.

Looking for general guides on how the protocol works? See the [Learn](learn/overview.md) section.

Looking for a user flow and how to launch a project? See the [Build](build/getting-started.md) section.

Looking for an outline of the protocol's API, or a deep dive on each line of code? Click around in the [Specifications](specifications/contracts/) section. There are bug bounties posted for each documented function.

Reach out to contributors of the DAO on Discord to call attention to something that could be made more clear. You can also submit PR's directly to the documentation repo [here](https://github.com/jbx-protocol/juice-docs).

Progress:

All contracts are fully documented. In the coming week there will be several more deep dive tutorials written about how you can use `IJBFundingCycleDataSource` , `IJBPayDelegate`, `IJBRedemptionDelegate`, `IJBSplitAllocator`, and `IJBFundingCycleBallot `to pull off creative custom treasury strategies, and posts explaining how to forward funds from your own smart contracts into Juicebox projects.
