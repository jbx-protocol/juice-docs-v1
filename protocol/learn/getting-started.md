---
description: How to begin interacting with the protocol.
---

# Getting started

The first transaction to call when getting started is [`JBController.launchProjectFor(...)`](../contracts/or-controllers/jbcontroller/write/launchprojectfor.md).&#x20;

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

Once a project has been created, it can begin accepting funds. ETH can be sent to the project by calling [`JBETHPaymentTerminal.pay(...)`](../contracts/or-payment-terminals/jbethpaymentterminal/write/pay-1.md).

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

