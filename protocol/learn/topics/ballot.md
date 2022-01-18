# Ballot

#### What everyone needs to know

* A ballot is a custom contract that adheres to `IJBFundingCycleBallot` which can be attached to a project's funding cycles to create restrictive conditions according to which proposed funding cycle reconfigurations must follow in order to take effect.
* A project's funding cycle reconfigurations can vary widely. Custom ballots can be useful to keep changes in check.
* A ballot contract must implement a function that tells the world if a state of a proposed reconfiguration is `active`, `approved`, or `failed`.
* If a reconfiguration fails to be approved by a ballot, it will not be used. Instead, a copy of the current funding cycle will be used.

#### What you'll want to know if you're building

* _wip_
