# JBSplit

Used in:

* [`JBSplitStore`](../contracts/jbsplitstore/)
  * [`set`](../contracts/jbsplitstore/write/set.md)

Definition:

```solidity
struct JBSplit {
  // A flag that only has effect if a projectId is also specified, and that project has issued its tokens. 
  // If so, this flag indicates if the tokens that result from making a payment to the project should be delivered staked or unstaked to the beneficiary.
  bool preferUnstaked;
  // The percent of the whole group that this split occupies. This number is out of 10000.
  uint16 percent;
  // Specifies if the split should be unchangeable until the specifies time comes, with the exception of extending the lockedUntil period.
  uint48 lockedUntil;
  // The role the  beneficary depends on whether or not projectId is specified, or whether or not allocator is specified. 
  // If allocator is set, the beneficiary will be forwarded to the allocator for it to use.
  // If allocator is not set but projectId is set, the beneficiary is the address to which the project's tokens will be sent that result from a payment to it. 
  // If neither allocator or projectId are set, the beneficiary is where the funds from the split will be sent.
  address payable beneficiary;
  // If an allocator is specified, funds will be sent to the allocator contract along with the projectId, beneficiary, preferUnstaked properties.
  IJBSplitAllocator allocator;
  // If an allocator is not set but a projectId is set, funds will be sent to the Juicebox treasury belonging to the project who's ID is specified. 
  // Resulting tokens will be routed to the beneficiary with the unstaked token prerence respected.
  uint56 projectId;
}
```

* `preferUnstaked` is a flag that only has effect if a `projectId` is also specified, and that project has [`issued`](../contracts/jbtokenstore/write/issuefor.md)its tokens. If so, this flag indicates if the tokens that result from making a payment to the project should be delivered staked or unstaked to the `beneficiary`.
* `percent` is the percent of the whole group that this split occupies. This number is out of 10000.
* `lockedUntil` specifies if the split should be unchangeable until the specifies time comes, with the exception of extending the `lockedUntil` period.
*   The role the  `beneficary` depends on whether or not `projectId` is specified, or whether or not `allocator` is specified. If `allocator` is set, the `beneficiary` will be forwarded to the `allocator` for it to use. `pallocator` is not set but 

    If `projectId` is set, the `beneficiary` is the address to which the project's tokens will be sent that result from a payment to it. If neither  `allocator` nor `projectId`are set, the `beneficiary` is where the funds from the split will be sent.
* If an `allocator` is specified, funds will be sent to the `allocator` contract along with the `projectId`, `beneficiary`, `preferUnstaked` properties.
* If an `allocator` is not set but a `projectId `is set, funds will be sent to the Juicebox treasury belonging to the project who's ID is specified. 
