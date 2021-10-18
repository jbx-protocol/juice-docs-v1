---
description: How to begin interacting with the protocol.
---

# Getting started

The first transaction to call when getting started is [`JBController.launchProjectFor(...)`](../contracts/or-controllers/jbcontroller/write/launchprojectfor.md).

```solidity
function launchProjectFor(
  address _owner,
  bytes32 _handle,
  string calldata _uri,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBOverflowAllowance[] memory _overflowAllowances,
  JBSplit[] memory _payoutSplits,
  JBSplit[] memory _reservedTokenSplits,
  IJBTerminal _terminal
) external { ... }
```

This function is for convenience. It wraps the following 5 transactions into one, preventing you from having to call them sequentially:

* [`JBProjects.createFor(...)`](../contracts/jbprojects/write/createfor.md)
* [`JBDirectory.setControllerOf(...)`](../contracts/jbdirectory/write/setcontrollerof.md)
* [`JBDirectory.addTerminalOf(...)`](../contracts/jbdirectory/write/addterminalof.md)
* [`JBFundingCycleStore.configureFor(...)`](../contracts/jbfundingcyclestore/write/configurefor.md)
* [`JBSplitStore.set(...)`](../contracts/jbsplitstore/write/set.md)

At any time after the project has been created, it's owner can issue ERC-20 tokens for the protocol to use as its treasury token by calling [`JBTokenStore.issueFor(...)`](../contracts/jbtokenstore/write/issuefor.md). By default the protocol uses an internal accounting mechanism to account for projects' tokens. Once ERC-20's are issued by a project, anyone can claim these internal tokens into the token holders wallet as ERC-20's on their behalf by calling [`JBTokenStore.claimFor(...)`](../contracts/jbtokenstore/write/claimfor.md).

```solidity
  function issueFor(
    uint256 _projectId,
    string calldata _name,
    string calldata _symbol
  )
    external
    override
    requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.ISSUE)
    returns (IJBToken token) { ... }
```

Once a project has been created, it can begin accepting funds from anyone. ETH can be sent to the project by calling [`JBETHPaymentTerminal.pay(...)`](../contracts/or-payment-terminals/jbethpaymentterminal/write/pay-1.md).

```solidity
function pay(
 uint256 _projectId,
  address _beneficiary,
  uint256 _minReturnedTokens,
  bool _preferClaimedTokens,
  string calldata _memo,
  bytes calldata _delegateMetadata
 ) external payable override returns (uint256 fundingCycleId) { ... }
```

At any point, anyone can distribute a project's funds up to its current funding cycle's target to its preprogrammed splits by calling [`JBETHPaymentTerminal.distributePayoutsOf(...)`](../contracts/or-payment-terminals/jbethpaymentterminal/write/distributepayoutsof.md).

```solidity
function distributePayoutsOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  string memory _memo
) external override nonReentrant returns (uint256) { ... }
```

A project's owner can also distribute additional funds from its treasury's overflow up until its preconfigured allowance.

```solidity
function useAllowanceOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  address payable _beneficiary
)
  external
  override
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.USE_ALLOWANCE)
  returns (uint256) { ... }
```

A project's owner can mint or burn its token by calling [`JBController.mintTokensOf(...)`](../contracts/jbtokenstore/write/mintfor.md) and [`JBController.burnFrom(...)`](../contracts/jbtokenstore/write/burnfrom.md).

```solidity
function mintTokensOf(
  uint256 _projectId,
  uint256 _tokenCount,
  address _beneficiary,
  string calldata _memo,
  bool _preferClaimedTokens,
  bool _shouldReserveTokens
)
  external
  override
  nonReentrant
  requirePermissionAllowingOverride(
    projects.ownerOf(_projectId),
    _projectId,
    JBOperations.MINT,
    directory.isTerminalDelegateOf(_projectId, msg.sender)
  ) { ... }
```

```solidity
function burnTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  string calldata _memo,
  bool _preferClaimedTokens
)
  external
  override
  nonReentrant
  requirePermissionAllowingOverride(
    _holder,
    _projectId,
    JBOperations.BURN,
    directory.isTerminalDelegateOf(_projectId, msg.sender)
  ) { ... }
```

At any point, anyone can distribute a project's reserved tokens to the preprogrammed addresses by calling [`JBController.distributeReservedTokensOf(...)`](../contracts/or-controllers/jbcontroller/write/distributereservedtokensof.md).

```solidity
function distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  external
  nonReentrant
  returns (uint256) { ... }
```

Anyone who holds your project's tokens can redeem them for a proportional share of the project's overflow by calling [`JBETHPaymentTerminal.redeemTokensOf(...)`](../contracts/or-payment-terminals/jbethpaymentterminal/write/redeemtokensof.md). The overflow amount is the treasury's balance minus the current funding cycle's target.&#x20;

Redeeming tokens burns them, and allows them your token holders to exit the community at any time with their share of the funds.

```solidity
function redeemTokensOf(
  address _holder,
  uint256 _projectId,
  uint256 _tokenCount,
  uint256 _minReturnedWei,
  address payable _beneficiary,
  string memory _memo,
  bytes memory _delegateMetadata
)
  external
  override
  nonReentrant
  requirePermission(_holder, _projectId, JBOperations.REDEEM)
  returns (uint256 claimAmount) { ... }
```

A project's owner can reconfigure its project's funding cycle at any time by calling [`JBController.reconfigureFundingCyclesOf(...)`](../contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md). If the project is in the middle of a funding cycle, the update will be queued to take effect next cycle. If the current funding cycle has an attached ballot contract, the reconfiguration must be approved by it before taking effect.

```solidity
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBOverflowAllowance[] memory _overflowAllowances,
  JBSplit[] memory _payoutSplits,
  JBSplit[] memory _reservedTokenSplits
)
  external
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256) { ... }
```

At any point, anyone can inject funds into a project's treasury by calling [`JBETHPaymentTerminal.addToBalanceOf(...)`](../contracts/or-payment-terminals/jbethpaymentterminal/write/addtobalanceof.md).

```solidity
 function addToBalanceOf(uint256 _projectId, string memory _memo) external payable override { ... }
```
