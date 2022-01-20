# Reconfiguration

{% hint style="info" %}
This section is only visible if a project has funding cycles (see [funding.md](funding.md "mention")).
{% endhint %}

**Reconfiguration** allows project owners to set a minimum amount of time before Juicebox project reconfigurations can go into effect.

* With _No strategy_ selected, the project owner can reconfigure the project immediately before a new funding cycle begins (see [funding.md](funding.md "mention")). This can make a project vulnerable to being rug-pulled by the owner.
* With _7-day delay_ selected, the project owner must submit reconfigurations at least 7 days before the start of a funding cycle. If submitted after this deadline, the reconfigurations will go into effect the following cycle.
* With _3-day delay_ selected, the project owner must submit reconfigurations at least 3 days before the start of a funding cycle. If submitted after this deadline, the reconfigurations will go into effect the following cycle.
* _Custom strategy_ allows you to create a custom strategy by entering the address of an Ethereum smart contract that implements [this interface](https://github.com/jbx-protocol/juice-contracts-v1/blob/05828d57e3a27580437fc258fe9041b2401fc044/contracts/FundingCycles.sol).
