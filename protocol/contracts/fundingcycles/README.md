# FundingCycles



| Contract | Description |
| :--- | :--- |
| **\`\`**[**`TerminalV2PaymentLayer`**](../terminalv2paymentlayer/)**\`\`** | Manages all inflows and outflows of funds into the Juicebox ecosystem. This contract also stores all treasury funds for all projects. |
| **\`\`**[**`TerminalV2DataLayer`**](../terminalv2datalayer/)**\`\`** | Stitches together funding cycles and treasury tokens, making sure all activity is accounted for and correct. |
| **\`\`**[**`TerminalDirectory`**](../terminaldirectory/)**\`\`** | Keeps a reference of which terminal contract each project is currently accepting funds through. When a project migrates from one terminal to another, the change is reflected in this contract.  |
| **\`\`**[**`TicketBooth`**](../ticketbooth/)**\`\`** | Manages token minting and burning for all projects, while keeping track of staked/unstaked and locked/unlock token balances for all accounts. |
| \*\*\*\*[**`FundingCycles`**](./)**\`\`** | Manage funding cycle configurations, ballots, accounting, and scheduling. |
| **\`\`**[**`SplitStore`**](../splitstore/)**\`\`** | Stores splits information for all groups of each project. Projects can create split groups for directing percents of a total token allocation to any address, any other Juicebox project, or any contract that inherits from the ISplitAllocator interface. |
| **\`\`**[**`OperatorStore`**](../operatorstore/)**\`\`** | Stores operator permissions for all addresses. Addresses can give permissions to any other address to take specific actions throughout the Juicebox ecosystem on their behalf. |
| **\`\`**[**`Prices`**](../prices/)**\`\`** | Manage and normalizes ETH price feeds. |

A full architecture diagram showing the paths through which each contract's transactions can be called is found [here](https://www.figma.com/file/YIf64bRfSXjCDSPb49uAwv/Juicebox-Technical-Docs-Copy?node-id=262%3A8).

![](../../../.gitbook/assets/architecture%20%282%29.png)

