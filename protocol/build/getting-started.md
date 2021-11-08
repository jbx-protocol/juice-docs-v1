---
description: How to begin interacting with the protocol.
---

# Getting started

The first transaction to call when getting started is [`JBController.launchProjectFor(...)`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md).

```solidity
function launchProjectFor(
  address _owner,
  bytes32 _handle,
  string calldata _metadataCid,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  JBGroupedSplits[] memory _groupedSplits,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
```

At any time after the project has been created, its owner can issue ERC-20 tokens for the protocol to use as its community token by calling [`JBTokenStore.issueFor(...)`](../specifications/contracts/jbtokenstore/write/issuefor.md). By default the protocol uses an internal accounting mechanism to account for projects' tokens. 

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

Once a project has been created, it can begin accepting funds from anyone. ETH can be sent to the project by calling [`JBETHPaymentTerminal.pay(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/pay.md).

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

At any point, anyone can distribute a project's funds up to its current funding cycle's target to its preprogrammed splits by calling [`JBETHPaymentTerminal.distributePayoutsOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/distributepayoutsof.md).

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

A project's owner can mint more of its token by calling [`JBController.mintTokensOf(...)`](../specifications/contracts/jbtokenstore/write/mintfor.md). Anyone can burn their tokens by calling [`JBController.burnFrom(...)`](../specifications/contracts/jbtokenstore/write/burnfrom.md).

```solidity
function mintTokensOf(
  uint256 _projectId,
  uint256 _tokenCount,
  address _beneficiary,
  string calldata _memo,
  bool _preferClaimedTokens,
  uint256 _reservedRate 
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

At any point, anyone can distribute a project's reserved tokens to the preprogrammed addresses by calling [`JBController.distributeReservedTokensOf(...)`](../specifications/contracts/or-controllers/jbcontroller/write/distributereservedtokensof.md).

```solidity
function distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  external
  nonReentrant
  returns (uint256) { ... }
```

Anyone who holds your project's tokens can burn them for a proportional share of the project's overflow by calling [`JBETHPaymentTerminal.redeemTokensOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/redeemtokensof.md). The overflow amount is the treasury's balance minus the current funding cycle's target.

Redeeming tokens allows your token holders to exit the community at any time with their share of the funds.

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

A project's owner can reconfigure its project's funding cycle at any time by calling [`JBController.reconfigureFundingCyclesOf(...)`](../specifications/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md). If the project is in the middle of a funding cycle, the update will be queued to take effect next cycle. If the current funding cycle has an attached ballot contract, the reconfiguration must be approved by it before taking effect.

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
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256) { ... }
```

At any point, anyone can inject funds into a project's treasury by calling [`JBETHPaymentTerminal.addToBalanceOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/addtobalanceof.md).

```solidity
 function addToBalanceOf(uint256 _projectId, string memory _memo) external payable override { ... }
```

If a project has issued its ERC-20's, anyone can claim tokens that are being represented via the internal accounting mechanism into the token holders wallet as ERC-20's on their behalf by calling [`JBTokenStore.claimFor(...)`](../specifications/contracts/jbtokenstore/write/claimfor.md).

```solidity
function claimFor(
    address _holder,
    uint256 _projectId,
    uint256 _amount
  ) external { ... }
```
