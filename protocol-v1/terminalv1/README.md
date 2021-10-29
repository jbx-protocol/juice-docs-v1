---
description: 'Interfaces the contracts and the user, working as a top-level contract.'
---

# TerminalV1

## Content

On this page you'll find the documentation for the constructor and the events, for the read and write functions of the terminal follow the links:

{% page-ref page="read.md" %}

{% page-ref page="write.md" %}

## Constructor

**Params:**

*   **\_projects**: A Projects contract which mints ERC-721's that represent project ownership and transfers**.** 
*   **\_fundingCycles**: A funding cycle configuration store.
*   **\_ticketBooth**: A contract that manages Ticket printing and redeeming.
*   **\_operatorStore**: A contract storing operator assignments.
*   **\_modStore**: A storage for a project's mods.
*   **\_prices**: A price feed contract to use.
*   **\_terminalDirectory**: A directory of a project's current Juicebox terminal to receive payments in.

```text

constructor(
    IProjects _projects,
    IFundingCycles _fundingCycles,
    ITicketBooth _ticketBooth,
    IOperatorStore _operatorStore,
    IModStore _modStore,
    IPrices _prices,
    ITerminalDirectory _terminalDirectory,
    address payable _governance
)
```

## Events



