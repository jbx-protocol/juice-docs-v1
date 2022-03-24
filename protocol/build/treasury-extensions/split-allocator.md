# Split allocator

Before implementing, learn about allocators [here](../../learn/glossary/allocator.md), and splits [here](../../learn/glossary/splits.md).
### Specs

A contract can become a split allocator by adhering to [`IJBSplitAllocator`](../../api/interfaces/ijbsplitallocator.md):

```solidity
interface IJBSplitAllocator {
  function allocate(
    uint256 _amount,
    uint256 _projectId,
    uint256 _group,
    JBSplit calldata _split
  ) external payable;
}
```

When extending the payout distribution or reserved token distribution functionality with an allocator, the protocol will pass a [`JBSplitAllocatorData`](../../api/data-structures/jbsplitallocatordata.md) to the `allocate(...)` function:

```solidity
```

```solidity
struct JBSplit {
  // A flag that only has effect if a projectId is also specified, and the project has a token contract attached.
  // If so, this flag indicates if the tokens that result from making a payment to the project should be delivered claimed into the beneficiary's wallet, or unclaimed to save gas.
  bool preferClaimed;
  // The percent of the whole group that this split occupies. This number is out of `JBConstants.SPLITS_TOTAL_PERCENT`.
  uint256 percent;
  // If an allocator is not set but a projectId is set, funds will be sent to the protocol treasury belonging to the project who's ID is specified.
  // Resulting tokens will be routed to the beneficiary with the claimed token preference respected.
  uint256 projectId;
  // The role the of the beneficary depends on whether or not projectId is specified, and whether or not an allocator is specified.
  // If allocator is set, the beneficiary will be forwarded to the allocator for it to use.
  // If allocator is not set but projectId is set, the beneficiary is the address to which the project's tokens will be sent that result from a payment to it.
  // If neither allocator or projectId are set, the beneficiary is where the funds from the split will be sent.
  address payable beneficiary;
  // Specifies if the split should be unchangeable until the specified time, with the exception of extending the locked period.
  uint256 lockedUntil;
  // If an allocator is specified, funds will be sent to the allocator contract along with all properties of this split.
  IJBSplitAllocator allocator;
}
```

The `msg.sender` to the allocator will either be the payment terminal that facilitated the payout distribution, or the controller that facilitated the reserved tokens distribution.

In payment terminals based on the [`JBPayoutRedemptionPaymentTerminal`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal), such as [`JBETHPaymentTerminal`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/)'s and [`JBERC20PaymentTerminal`](../../api/contracts/or-payment-terminals/jberc20paymentterminal/)'s, the allocator hook gets called while the payouts are being distributed to splits. [View the docs](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/_distributetopayoutsplitsof.md). 

* If the allocation is coming from an ETH payment terminal such as [`JBETHPaymentTerminal`](../../api/contracts/or-payment-terminals/jbethpaymentterminal/), the ETH will be included in the call to `allocate(...)`. 
* If the allocation is coming from an ERC20 payment terminal such as [`JBERC20PaymentTerminal`](../../api/contracts/or-payment-terminals/jberc20paymentterminal/), the tokens will be pre-approved for the allocator contract to transfer them to it. Make sure to initiate the transfer, and make sure to not leave allocated tokens stuck in the allocator contract.
* If the allocation is coming from a controller such as [`JBController`](../../api/contracts/or-controllers/jbcontroller/) distributing reserved tokens, the tokens will be pre-distributed to the address as unclaimed tokens in the [`JBTokenStore`](../../api/contracts/jbtokenstore/) from which the allocator can then [`claimFor(...)`](../../api/contracts/jbtokenstore/write/claimfor.md) itself or [`transferFrom(...)`](../../api/contracts/jbtokenstore/write/transferfrom.md) itself to another address. Make sure to not leave allocated tokens stuck in the allocator contract or unclaimed in the `JBTokenStore` contract.

### Attaching

An allocator contract should be deployed independently. Once deployed, it's address can be configured into a project's payout splits or reserved token splits so that any distribution triggered while the funding cycle is active sends the relevant token to the allocator contract's `allocat(...)` hook. 