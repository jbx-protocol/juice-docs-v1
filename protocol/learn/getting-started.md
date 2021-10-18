---
description: How to begin interacting with the protocol
---

# Getting started

The first transaction to call when getting started is [`JBController.launchProjectFor`](../contracts/or-controllers/jbcontroller/write/launchprojectfor.md).&#x20;

```solidity
 function launchProjectFor(
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
