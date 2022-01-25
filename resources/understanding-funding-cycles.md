# Understanding Funding Cycles

Every Juicebox project has **Funding Cycles**, which can be used to synchronize governance, payouts, and project reconfiguration.

Juicebox projects have the option to set a _Funding cycle duration_. If a funding cycle duration is set, **project parameters can not be changed during a funding cycle**. Instead, reconfigurations are queued to be executed at the start of the next funding cycle.

{% hint style="info" %}
Projects with no funding cycle duration can make project reconfigurations at any time, which will trigger a new funding cycle.
{% endhint %}

Projects can also set a _Funding cycle target ****_ in ETH or USD, which determines how much funding can be distributed each funding cycle. Contributor payouts and fixed monthly costs should be considered when setting this target. **Overflow** is created if a project exceeds this target. Token holders can then redeem their tokens to claim overflow, burning their tokens in the process.

