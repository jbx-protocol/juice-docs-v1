# TerminalDirectory

## Address

Mainnet:[`0x46C9999A2EDCD5aA177ed7E8af90c68b7d75Ba46`](https://etherscan.io/address/0x46c9999a2edcd5aa177ed7e8af90c68b7d75ba46)

Kovan: `0x71BA69044CbD951AC87124cBEdbC0334AB21F26D`

## Contract

[contracts/TerminalDirectory.sol](https://github.com/jbx-protocol/juicehouse/blob/main/packages/hardhat/contracts/TerminalDirectory.sol)

## Purpose

Stores the active Terminal for each project.

Projects can deploy contracts that will forward a direct payment to the Terminal.

Projects can deploy contracts that will forward a direct payment to the Terminal.

### Events

```
event DeployAddress( 
    uint256 indexed projectId, 
    string memo, 
    address indexed caller 
)
```

```
event SetTerminal(
    uint256 indexed projectId,
    ITerminal indexed terminal,
    address caller
)
```

```
event SetPayerPreferences(
    address indexed account,
    address beneficiary,
    bool preferUnstakedTickets
)
```

### Constructor

```javascript
/** 
  @param _projects A Projects contract which mints ERC-721's that represent project ownership and transfers.
  @param _operatorStore A contract storing operator assignments.
*/
constructor(IProjects _projects, IOperatorStore _operatorStore)
    Operatable(_operatorStore)
```

### Read

```javascript
/// @notice The Projects contract which mints ERC-721's that represent project ownership and transfers.
function projects() external view returns (IProjects)
```

```javascript
/// @notice For each project ID, the juicebox terminal that the direct payment addresses are proxies for.
function terminalOf(uint256 _projectId) external view returns (ITerminal)
```

```javascript
/// @notice For each address, the address that will be used as the beneficiary of direct payments made.
function beneficiaryOf(address _account) external returns (address)
```

```javascript
/// @notice For each address, the preference of whether ticket will be auto claimed as ERC20s when a payment is made.
function unstakedTicketsPreferenceOf(address _account)
    external
    returns (bool)
```

```javascript
/** 
  @notice 
  A list of all direct payment addresses for the specified project ID.

  @param _projectId The ID of the project to get direct payment addresses for.

  @return A list of direct payment addresses for the specified project ID.
*/
function addressesOf(uint256 _projectId)
    external
    view
    override
    returns (IDirectPaymentAddress[] memory)
{
    return _addressesOf[_projectId];
}
```

## Write

```javascript
/** 
  @notice 
  Allows anyone to deploy a new direct payment address for a project.

  @param _projectId The ID of the project to deploy a direct payment address for.
  @param _memo The note to use for payments made through the new direct payment address.
*/
function deployAddress(uint256 _projectId, string calldata _memo)
    external
```

```javascript
/** 
  @notice 
  Update the juicebox terminal that payments to direct payment addresses will be forwarded for the specified project ID.

  @param _projectId The ID of the project to set a new terminal for.
  @param _terminal The new terminal to set.
*/
function setTerminal(uint256 _projectId, ITerminal _terminal)
    external
```

```javascript
/** 
  @notice 
  Allows any address to pre set the beneficiary of their payments to any direct payment address,
  and to pre set whether to prefer to unstake tickets into ERC20's when making a payment.

  @param _beneficiary The beneficiary to set.
  @param _preferUnstakedTickets The preference to set.
*/
function setPayerPreferences(
    address _beneficiary,
    bool _preferUnstakedTickets
) external
```
