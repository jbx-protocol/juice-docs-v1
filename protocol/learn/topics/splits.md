# Splits

#### What everyone needs to know

* A project can store splits for an arbitrary number of groups, such as for payout distributions or for reserved token distributions.
* A split can specify an address, a juicebox project, or a contract that adheres to the `IJBSplitAllocator` interface as its recipient.
* By default, splits can be changed at any time for any funding cycle configuration. A project's owner can also independently lock a split to a funding cycle configuration for a customizable duration.

#### What you'll want to know if you're building

_wip_