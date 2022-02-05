---
description: How to begin interacting with the protocol.
---

# Getting started

The first transaction to call when getting started is [`JBController.launchProjectFor(...)`](../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md).

```solidity
function launchProjectFor(
  address _owner,
  JBProjectMetadata calldata _projectMetadata,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
```

<details>

<summary>View project info</summary>

Launching a project will mint a new NFT in the [`JBProjects`](../specifications/contracts/jbprojects/) contract. The owner can be found using [`JBProjects.ownerOf(...)`](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#IERC721-ownerOf-uint256-).

```solidity
function ownerOf(uint256 _projectId) external returns (address owner) { ... }
```

The project's metadata can be found using [`JBProjects.metadataContentOf(...)`](../specifications/contracts/jbprojects/properties/metadatacontentof.md).

```solidity
function metadataContentOf(uint256 _projectId, uint256 _domain)
  external
  view
  returns (string memory) { ... }
```

</details>

<details>

<summary>View funding cycles</summary>

Funding cycle data can be found in the [`JBFundingCycleStore`](../specifications/contracts/jbfundingcyclestore/) contract. A funding cycle configuration can be found using [`JBFundingCycleStore.get(...)`](../specifications/contracts/jbfundingcyclestore/read/get.md), where `_configuration` is the block timestamp when the funding cycle was configured.

```solidity
function get(uint256 _projectId, uint256 _configuration)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

The project's current funding cycle can be found using [`JBFundingCycleStore.currentOf(...)`](../specifications/contracts/jbfundingcyclestore/read/currentof.md).

```solidity
function currentOf(uint256 _projectId)
  public
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

The project's queued funding cycle can be found using [`JBFundingCycleStore.queuedOf(...)`](../specifications/contracts/jbfundingcyclestore/read/queuedof.md). \
\
By default, the queued cycle is a copy of the current one that starts immediately afterwards, using a discounted weight. \
\
If the project has proposed a reconfiguration, the queued cycle will reflect the changes once they are approved by the current cycle's ballot. Reconfigurations during a funding cycle with no ballot are automatically queued.\
\
The project has no queued cycle if the current cycle has no duration.

```solidity
function queuedOf(uint256 _projectId)
  external
  view
  override
  returns (JBFundingCycle memory fundingCycle) { ... }
```

</details>

<details>

<summary>View splits</summary>

A project's splits data can be found in the [`JBSplitStore`](../specifications/contracts/jbsplitsstore) contract. A group of splits belonging to any particular domain during any particular funding cycle configuration can be found using  [`JBSplitStore.splitsOf(...)`](../specifications/contracts/jbsplitsstore/read/splitsof.md).

```solidity
function splitsOf(
  uint256 _projectId,
  uint256 _domain,
  uint256 _group
) external view override returns (JBSplit[] memory) { ... }
```

</details>

<details>

<summary>View fund access constraints</summary>

A project's fund access conatraints can found in the [`JBController`](../specifications/contracts/or-controllers/jbcontroller/) contract used to launch the project. It's distribution limit of any payment terminal during any funding cycle configuration can be found using [`JBController.distributionLimitOf(...)`](../specifications/contracts/or-controllers/jbcontroller/read/distributionlimitof.md). The currency being used for this distribution limit can be found using [`JBController.distributionLimitCurrencyOf(...)`](../specifications/contracts/or-controllers/jbcontroller/read/distributionlimitcurrencyof.md).

```solidity
function distributionLimitOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) { ... }
```

```solidity
function distributionLimitCurrencyOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) { ... }
```

It's overflow allowance from any payment terminal during any funding cycle configuration can be found using [`JBController.overflowAllowanceOf(...)`](../specifications/contracts/or-controllers/jbcontroller/read/overflowallowanceof.md). The currency being used for this overflow allowance can be found using [`JBController.overflowAllowanceCurrencyOf(...)`](../specifications/contracts/or-controllers/jbcontroller/read/overflowallowancecurrencyof.md).

```solidity
function overflowAllowanceOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) { ... }
```

```solidity
function overflowAllowanceCurrencyOf(
  uint256 _projectId,
  uint256 _configuration,
  IJBTerminal _terminal
) external view override returns (uint256) { ... }
```

</details>

<details>

<summary>View terminals and controller</summary>

The [`JBDirectory`](../specifications/contracts/jbdirectory) contract stores addresses of payment terminals where a project is currently accepting funds through. A projects currently set terminals can be found using [`JBDirectory.terminalsOf(...)`](../specifications/contracts/jbdirectory/read/terminalsof.md).

```solidity
function terminalsOf(uint256 _projectId) external view override returns (IJBTerminal[] memory) { ... }
```

If a project has multiple terminals for the same token, the primary terminal that it wishes to accept funds through of that token type can be found using [`JBDirectory.primaryTerminalOf(...)`](../specifications/contracts/jbdirectory/read/primaryterminalof.md).

```solidity
function primaryTerminalOf(uint256 _projectId, address _token)
  public
  view
  override
  returns (IJBTerminal) { ... }
```

The [`JBDirectory`](../specifications/contracts/jbdirectory) contract also stores the address of the controller that is managing a project's funding cycles and tokens. A projects current terminal can be found using [`JBDirectory.controllerOf(...)`](../specifications/contracts/jbdirectory/properties/controllerof.md).

```solidity
function controllerOf(uint256 _projectId) external view override returns (IJBController) { ... }
```
</details>

Once a project has been created, it can begin accepting funds from anyone. ETH can be sent to the project by calling [`JBETHPaymentTerminal.pay(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/pay.md).

```solidity
function pay(
 uint256 _projectId,
  address _beneficiary,
  uint256 _minReturnedTokens,
  bool _preferClaimedTokens,
  string calldata _memo,
  bytes calldata _delegateMetadata
 ) external payable override { ... }
```

<details>

<summary>View treasury balance</summary>

A project's treasury balance can be found in the respective terminal store contracts. For example, in the [`JBETHPaymentTerminalStore`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore), the balance can be found using [`JBETHPaymentTerminalStore.balanceOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore/properties/balanceof.md).

```solidity
function balanceOf(uint256 _projectId) external view override returns (uint256) { ... }
```

The project's current overflow can also be found in the respective terminal store contracts. For example, in the [`JBETHPaymentTerminalStore`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore), the terminal's overflow can be found using [`JBETHPaymentTerminalStore.currentOverflowOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore/read/currentoverflowof.md).

```solidity
function currentOverflowOf(uint256 _projectId) external view returns (uint256) { ... }
```

A terminal store can also resolve the total amount of overflow in all of a project's terminals. For example, in the [`JBETHPaymentTerminalStore`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore), the project's overall overflow can be found using [`JBETHPaymentTerminalStore.currentTotalOverflowOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore/read/currenttotaloverflowof.md).

```solidity
function currentTotalOverflowOf(uint256 _projectId) external view returns (uint256) { ... }
```

</details>

<details>

<summary>View project token distribution</summary>

Each holder's balance of a project's token can be found in the [`JBTokenStore`](../specifications/contracts/jbtokenstore) contract. The balance can be found using [`JBTokenStore.balanceOf(...)`](../specifications/contracts/jbtokenstore/read/balanceof.md).

```solidity
function balanceOf(address _holder, uint256 _projectId) external view returns (uint256 _result)
```

The project token's total supply can also be found in the [`JBTokenStore`](../specifications/contracts/jbtokenstore) contract using [`JBTokenStore.totalSupplyOf(...)`](../specifications/contracts/jbtokenstore/read/totalsupplyof.md)

```solidity
function totalSupplyOf(uint256 _projectId) external view returns (uint256)
```

</details>

<details>

<summary>View reserved token balance</summary>

A project's undistributed reserved token balance can be found in the project's current controller. For example in the [`JBController`](../specifications/contracts/or-controllers/jbcontroller/), the used can be found using [`JBController.reservedTokenBalanceOf(...)`](../specifications/contracts/or-controllers/jbcontroller/read/reservedtokenbalanceof.md).

```solidity
function reservedTokenBalanceOf(uint256 _projectId, uint256 _reservedRate)
  external
  view
  returns (uint256) { ... }
```

</details>

At any point, anyone can distribute a project's funds up to its current funding cycle's distribution limit to its preprogrammed payout splits by calling [`JBETHPaymentTerminal.distributePayoutsOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/distributepayoutsof.md).

```solidity
function distributePayoutsOf(
  uint256 _projectId,
  uint256 _amount,
  uint256 _currency,
  uint256 _minReturnedWei,
  string memory _memo
) external override nonReentrant returns (uint256) { ... }
```

<details>

<summary>View used distribution limit</summary>

Any used distribution limit can be found in the respective terminal store contracts. For example, in the [`JBETHPaymentTerminalStore`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore), the used distribution limit during a funding cycle can be found using [`JBETHPaymentTerminalStore.usedDistributionLimitOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore/properties/useddistributionlimitof.md).

```solidity
function usedDistributionLimitOf(
  uint256 _projectId,
  uint256 _number
) external view override returns (uint256) { ... }
```

The remaining allowed distribution a project can make from a terminal during a funding cycle can be found using [`JBETHPaymentTerminal.remainingDistributionLimitOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/read/remainingdistributionlimitof.md).
</details>

A project's owner can also distribute additional funds from its treasury's overflow up until its preconfigured allowance.

```solidity
function remainingDistributionLimitOf(
    uint256 _projectId,
    uint256 _fundingCycleConfiguration,
    uint256 _fundingCycleNumber
  ) external view override returns (uint256) { ... }
```

<details>

<summary>View used overflow allowance</summary>

Any used overflow allowance can also be found in the respective terminal store contracts. For example, in the [`JBETHPaymentTerminalStore`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore), the used overflow allowance during a funding cycle can be found using [`JBETHPaymentTerminalStore.usedOverflowAllowanceOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminalstore/properties/usedoverflowallowanceof.md).

```solidity
function usedOverflowAllowanceOf(
  uint256 _projectId,
  uint256 _configuration
) external view override returns (uint256) { ... }
```

</details>

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

At any point, anyone can distribute a project's reserved tokens to the project's preprogrammed reserved token splits by calling [`JBController.distributeReservedTokensOf(...)`](../specifications/contracts/or-controllers/jbcontroller/write/distributereservedtokensof.md).

```solidity
function distributeReservedTokensOf(uint256 _projectId, string memory _memo)
  external
  nonReentrant
  returns (uint256) { ... }
```

Anyone who holds your project's tokens can burn them for a proportional share of the project's overflow by calling [`JBETHPaymentTerminal.redeemTokensOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/redeemtokensof.md). The overflow amount is the treasury's balance minus the current funding cycle's distribution limit.

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
  returns (uint256 reclaimAmount) { ... }
```

A project's owner can reconfigure its project's funding cycle at any time by calling [`JBController.reconfigureFundingCyclesOf(...)`](../specifications/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md). If the project is in the middle of a funding cycle, the update will be queued to take effect next cycle. If the current funding cycle has an attached ballot contract, the reconfiguration must be approved by it before taking effect.

```solidity
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256) { ... }
```

<details>

<summary>View reconfiguration ballot status</summary>

Reconfigurations are subject to the approval of the ballot contract included in the current funding cycle. The current ballot state can be found using [`JBFundingCycleStore.ballotStateOf(...)`](../specifications/contracts/jbfundingcyclestore/read/currentballotstateof.md).

```solidity
function currentBallotStateOf(uint256 _projectId) external view override returns (JBBallotState) { ... } 
```

</details>

At any point, anyone can inject funds into a project's treasury by calling [`JBETHPaymentTerminal.addToBalanceOf(...)`](../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/addtobalanceof.md).

```solidity
 function addToBalanceOf(uint256 _projectId, string memory _memo) external payable override { ... }
```

At any time after the project has been created, its owner can issue ERC-20 tokens for the protocol to use as its community token by calling [`JBController.issueTokenFor(...)`](../specifications/contracts/jbtokenstore/write/issuetokenfor.md). By default the protocol uses an internal accounting mechanism to account for projects' tokens.

A project can instead bring their own token, so long as it adheres to the [`IJBToken`](../specifications/interfaces/ijbtoken.md) interface. They can do so by calling [`JBController.changeTokenFor(...)`](../specifications/contracts/jbtokenstore/write/changetokenfor.md) This makes it easy to use ERC-1155's or custom contracts, and to change tokens over time to acheive a more creative design.

```solidity
function issueTokenFor(
  uint256 _projectId,
  string calldata _name,
  string calldata _symbol
)
  external
  override
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.ISSUE)
  returns (IJBToken token) { ... }
```

```solidity
function changeTokenOf(
  uint256 _projectId,
  IJBToken _token,
  address _newOwner
)
  external
  nonReentrant
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.CHANGE_TOKEN) { ... }
```

<details>

<summary>View the project's token</summary>

The current token being used by the project can be found in the [`JBTokensStore`](../specifications/contracts/jbtokenstore/) contract using [`JBTokenStore.tokenOf(...)`](../specifications/contracts/jbtokenstore/properties/tokenof.md). This will return a zero address if the project hasn't yet issued tokens or changed into a custom token.

```solidity
function tokenOf(uint256 _projectId) external view override returns (IJBToken) { ... }
```

</details>

If a project has issued its ERC-20's or is using a custom `IJBToken`, anyone can claim tokens that are being represented via the internal accounting mechanism into the token holders wallet on their behalf by calling [`JBTokenStore.claimFor(...)`](../specifications/contracts/jbtokenstore/write/claimfor.md).

```solidity
function claimFor(
    address _holder,
    uint256 _projectId,
    uint256 _amount
  ) external { ... }
```

<details>

<summary>View a holder's uncliamed project token balance</summary>

The unclaimed balance for each project token holder can be found in the [`JBTokensStore`](../specifications/contracts/jbtokenstore/) contract using [`JBTokenStore.unclaimedBalanceOf(...)`](../specifications/contracts/jbtokenstore/properties/unclaimedbalanceof.md).

```solidity
function unclaimedBalanceOf(address _holder, uint256 _projectId) external view override returns (uint256) { ... }
```

A project's total supply of unclaimed tokens can be found using [`JBTokenStore.unclaimedTotalSupplyOf(...)`](../specifications/contracts/jbtokenstore/properties/unclaimedtotalsupplyof.md)

```solidity
function unclaimedTotalSupplyOf(uint256 _projectId) external view override returns (uint256) { ... }
```

</details>
