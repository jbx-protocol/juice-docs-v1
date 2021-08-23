---
description: 'Manage Ticket printing, redemption, and account balances.'
---

# TicketBooth

Tickets can be either represented internally staked, or as unstaked ERC-20s. This contract manages these two representations and the conversion between the two.  
The total supply of a project's tickets and the balance of each account are calculated in this contract.

{% page-ref page="read.md" %}

{% page-ref page="write.md" %}

### Constructor

**Params:**

* **\_projects:** A Projects contract which mints ERC-721's that represent project ownership and transfers.
* **\_operatorStore:** A contract storing operator assignments.
* **\_terminalDirectory:** A directory of a project's current Juicebox terminal to receive payments in.

```text
constructor(
    IProjects _projects,
    IOperatorStore _operatorStore,
    ITerminalDirectory _terminalDirectory
);
```



