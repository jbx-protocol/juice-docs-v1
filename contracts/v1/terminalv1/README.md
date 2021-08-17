---
description: 'Interfaces the contracts and the user, working as a top-level contract.'
---

# TerminalV1

Content

On this page you'll find the documentation for the constructor and the events, for the read and write functions of the terminal follow the links:

{% page-ref page="read.md" %}

{% page-ref page="write.md" %}

## Constructor

**Params:**

*   projects A Projects contract which mints ERC-721's that represent project ownership and transfers**.** 
*   \_fundingCycles A funding cycle configuration store.
*   A contract that manages Ticket printing and redeeming.
*   \_modStore A storage for a project's mods.
*   \_prices A price feed contract to use.
*   \_terminalDirectory A directory of a project's current Juicebox terminal to receive payments in.

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
) Operatable(_operatorStore) {
    require(
        _projects != IProjects(address(0)) &&
            _fundingCycles != IFundingCycles(address(0)) &&
            _ticketBooth != ITicketBooth(address(0)) &&
            _modStore != IModStore(address(0)) &&
            _prices != IPrices(address(0)) &&
            _terminalDirectory != ITerminalDirectory(address(0)) &&
            _governance != address(address(0)),
        "TerminalV1: ZERO_ADDRESS"
    );
    projects = _projects;
    fundingCycles = _fundingCycles;
    ticketBooth = _ticketBooth;
    modStore = _modStore;
    prices = _prices;
    terminalDirectory = _terminalDirectory;
    governance = _governance;
}
```



