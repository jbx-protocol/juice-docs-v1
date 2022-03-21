# JBFundingCycle

```solidity
struct JBFundingCycle {
  // The funding cycle number for each project.
  // Each funding cycle has a number that is an increment of the cycle that directly preceded it.
  // Each project's first funding cycle has a number of 1.
  uint256 number;
  // The timestamp when the parameters for this funding cycle were configured.
  // This value will stay the same for subsequent funding cycles that roll over from an originally configured cycle.
  uint256 configuration;
  // The `configuration` of the funding cycle with a `number` one less than this cycle's `number`.
  uint256 basedOn;
  // The timestamp marking the moment from which the funding cycle is considered active.
  // It is a unix timestamp measured in seconds.
  uint256 start;
  // The number of seconds the funding cycle lasts for, after which a new funding cycle will start.
  // A duration of 0 means that the funding cycle will stay active until the project owner explicitly issues a reconfiguration, at which point a new funding cycle will be triggered with the updated properties.
  // If the duration is greater than 0, a project owner cannot make changes to a funding cycle's parameters while it is active – any proposed changes will apply to the subsequent cycle.
  // If no changes are proposed, a funding cycle rolls over to another one with the same properties but new `start` timestamp and a discounted `weight`.
  uint256 duration;
  // An unsigned 60.18-decimal fixed-point number that contracts can use to base arbitrary calculations on.
  // For example, the `JBPaymentTerminalStore` uses this to determine how many tokens it should mint when a payment is received.
  uint256 weight;
  // A number from 0-1000000000 indicating by how much the `weight` of the subsequent funding cycle should be reduced, if the project owner hasn't configured the subsequent funding cycle with an explicit `weight`.
  // If it's 0, each funding cycle will have equal weight.
  // If the number is 900000000, the next funding cycle will have a 10% smaller weight.
  uint256 discountRate;
  // An address of a contract that says whether a proposed reconfiguration should be accepted or rejected.
  // It can be used to create rules around how a project owner can change funding cycle parameters over time.
  IJBFundingCycleBallot ballot;
  // Extra data that can be associated with a funding cycle.
  uint256 metadata;
}
```

* `number` is the funding cycle number for each project. Each funding cycle has a number that is an increment of the cycle that directly preceded it. Each project's first funding cycle has a number of 1.
* `configuration` is the timestamp when the parameters for this funding cycle were configured. This value will stay the same for subsequent funding cycles that roll over from an originally configured cycle.
* `basedOn` is the `configuration` of the funding cycle with a `number` one less than this cycle's `number`.
* `start` is the timestamp marking the moment from which the funding cycle is considered active. It is a unix timestamp measured in seconds.
* `duration` is the number of seconds the funding cycle lasts for, after which a new funding cycle will start. A duration of 0 means that the funding cycle will stay active until the project owner explicitly issues a reconfiguration, at which point a new funding cycle will be triggered with the updated properties. If the duration is greater than 0, a project owner cannot make changes to a funding cycle's parameters while it is active – any proposed changes will apply to the subsequent cycle. If no changes are proposed, a funding cycle rolls over to another one with the same properties but new `start` timestamp and a discounted `weight`. 
* `weight` is a number that contracts can use to base arbitrary calculations on.
* `discountRate` is a number from 0-1000000000 indicating by how much the `weight` of the subsequent funding cycle should be reduced, if the project owner hasn't configured the subsequent funding cycle with an explicit `weight`. If the number is 900000000, the next funding cycle will have a 10% smaller weight. If the number is 1000000001, the funding cycle is non-recurring so there cannot be a next cycle.
* `ballot` is an address of a contract that says whether a proposed reconfiguration should be accepted or rejected.
* `metadata` is extra data that can be associated with a funding cycle.
