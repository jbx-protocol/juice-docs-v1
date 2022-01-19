# Reserved tokens

#### What everyone needs to know

* Reserved tokens allow a project to guarentee that a percentage of all newly minted tokens from payments will be reserved to a list of preprogrammed `JBSplit`s. This percentage is referred to as the reserved rate.
* A project's reserved rate and reserved token splits can be reconfigured each funding cycle.
* Reserved token splits can be routed to addresses, the owners of other Juicebox projects, or to contracts that adhered to the `ISplitAllocator` interface. 
* Reserved tokens do not get minted automatically when a new payment is received, they instead must be explicitly distributed during the funding cycle which contains the reserved rate and splits that should be applied. If a funding cycle's reserved rate or splits change before the allocation is distributed, the new values will apply.

#### What you'll want to know if you're building

* _wip_
