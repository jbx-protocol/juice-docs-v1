# Funding cycle

#### What everyone needs to know

* A funding cycle defines the parameters according to which a project's treasury behaves over a specified period of time. The most important parameters are as follows:
  * `start`: the start timestamp is the moment when from which the funding cycle is considered active. It is a unix timestamp measured in seconds.
  * `duration`: the duration specifies the number of days the funding cycle lasts for, after which a new funding cycle will start. A duration of 0 means that the funding cycle will stay active until the project owner explicitly issues a reconfiguration, at which point a new funding cycle will be triggered with the updated properties. If the duration is greater than 0, a project owner cannot make changes to a funding cycle's parameters while it is active – any proposed changes will apply to the subsequent cycle. If no changes are proposed, a funding cycle rolls over to another one with the same properties but new `start` timestamp.
  * `target`: the target lets a project owner preprogram the amount of funds that can be distributed during the funding cycle. Once this amount is distributed, the project owner must wait until a new funding cycle to distribute more funds. The value is interpreted with 18 decimal places.
  * `tapped`: The tapped amount is the amount that has currently been distributed during the funding cycle. This value is at max the `target` amount.
  * `currency`: the currency that the `target` and `tapped` amounts are measured in. See [`JBCurrencies`](../../specifications/libraries/jbcurrencies.md) for current options.
  * `weight`: the weight is a number that contracts can use to base arbitrary calculations on. For example, the `JBETHPaymentTerminalStore` uses this to determine how many tokens it should mint when a payment is received.&#x20;
  * `discountRate`: the discount rate is a percentage by which the `weight` of the subsequent funding cycle should be reduced, if the project owner hasn't configured the subsequent funding cycle with an explicit `weight`.
  * `ballot`: the ballot is an address of a contract that says whether a proposed reconfiguration should be accepted or rejected. It can be used to create rules around how a project owner can change funding cycle parameters over time.

#### What you'll want to know if you're building

* A funding cycle is represented as a [`JBFundingCycle`](../../specifications/data-structures/jbfundingcycle.md) data structure.
* `configured`: the configured timestamp is moment when the project's owner submitted the parameters that this funding cycle uses. Once a funding cycle ends&#x20;
