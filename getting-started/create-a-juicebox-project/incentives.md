# Incentives

**Incentives** consists of _Discount rate_ and _Bonding curve rate._

* _Discount rate_ is a percentage that changes the cost of issuing **tokens** over time. Each funding cycle, the amount of tokens issued per ETH will decrease by the discount rate. Generally, this will reward people who fund your project earlier.
* _Bonding curve rate_ changes the amount of overflow that each token can be redeemed for. A _Bonding curve rate_ of 60% means that tokens can be redeemed for 60% of the value they correspond to. The other 40% remains in the treasury, increasing the value of other tokens and rewarding long-term token holders.

{% hint style="info" %}
As tokens are redeemed for ETH, the bonding curve continuously increases the value of remaining tokens in the treasury. As a result, the total amount of ETH redeemed will be slightly higher than a flat percentage would suggest. You can calculate the effects of the bonding curve with the following formula:

![](../../.gitbook/assets/Capture2.PNG)

In this formula, **x** represents the amount of tokens being redeemed, **s** represents the total token supply, **o** represents the current overflow, and **r** represents the bonding curve rate.
{% endhint %}

{% embed url="https://youtu.be/dxqc3yMqi5M" %}
Bonding Curve 101
{% endembed %}



