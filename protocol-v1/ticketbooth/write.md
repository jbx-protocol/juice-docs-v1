# Write

### issue

Issues an owner's ERC-20 Tickets that'll be used when unstaking tickets.\
Deploys an owner's Ticket ERC-20 token contract.

**Params:**

* _**projectId:** The ID of the project being issued tickets.
* **_name:** The ERC-20's name. " Juicebox ticket" will be appended.
* **_symbol:** The ERC-20's symbol. "j" will be prepended.

```
function issue(
    uint256 _projectId,
    string calldata _name,
    string calldata _symbol
) external;
```

### lock

Lock a project's tickets, preventing them from being redeemed and from converting to ERC20s.\
Only a ticket holder or an operator can lock its tickets.

**Params:**

* **_holder:** The holder to lock tickets from.
* **_projectId:** The ID of the project whos tickets are being locked.
* **_amount:** The amount of tickets to lock.

```
function lock(
    address _holder,
    uint256 _projectId,
    uint256 _amount
) external;
```

### unlock

Unlock a project's tickets.\
The address that locked the tickets must be the address that unlocks the tickets.

**Params:**

* **_holder:** The holder to unlock tickets from.
* **_projectId:** The ID of the project whos tickets are being unlocked.
* **_amount:** The amount of tickets to unlock.

```
function unlock(
    address _holder,
    uint256 _projectId,
    uint256 _amount
) external;
```

### print

Print new tickets.\
Only a project's current terminal can print its tickets.

**Params:**

* **_holder:** The address receiving the new tickets.
* **_projectId:** The project to which the tickets belong.
* **_amount:** The amount to print.
* **_preferUnstakedTickets:** Whether ERC20's should be converted automatically if they have been issued.

```
function print(
    address _holder,
    uint256 _projectId,
    uint256 _amount,
    bool _preferUnstakedTickets
) external;
```

### redeem

Redeems tickets.\
Only a project's current terminal can redeem its tickets.

**Params:**

* **_holder:** The address that owns the tickets being redeemed.
* **_projectId:** The ID of the project of the tickets being redeemed.
* **_amount:** The amount of tickets being redeemed.
* **_preferUnstaked:** If the preference is to redeem tickets that have been converted to ERC-20s.

```
function redeem(
    address _holder,
    uint256 _projectId,
    uint256 _amount,
    bool _preferUnstaked
) external;
```

### stake

Stakes ERC20 tickets by burning their supply and creating an internal staked version.\
Only a ticket holder or an operator can stake its tickets.

**Params:**

* **_holder:** The owner of the tickets to stake.
* **_projectId:** The ID of the project whos tickets are being staked.
* **_amount:** The amount of tickets to stake.

```
function stake(
    address _holder,
    uint256 _projectId,
    uint256 _amount
) external;
```

### unstake

Unstakes internal tickets by creating and distributing ERC20 tickets.\
Only a ticket holder or an operator can unstake its tickets.

**Params:**

* **_holder:** The owner of the tickets to unstake.
* **_projectId:** The ID of the project whos tickets are being unstaked.
* **_amount:** The amount of tickets to unstake.

```
function unstake(
    address _holder,
    uint256 _projectId,
    uint256 _amount
) external;
```

### transfer

Allows a ticket holder to transfer its tickets to another account, without unstaking to ERC-20s.\
Only a ticket holder or an operator can transfer its tickets.

**Params:**

* **_holder:** The holder to transfer tickets from.
* **_projectId:** The ID of the project whos tickets are being transferred.
* **_amount:** The amount of tickets to transfer.
* **_recipient:** The recipient of the tickets.

```
function transfer(
    address _holder,
    uint256 _projectId,
    uint256 _amount,
    address _recipient
) external;
```
