# Understanding Tokens

When someone pays a Juicebox Project, they are rewarded with **tokens**.

If your project has a funding target, any additional funds received are considered **overflow**. Tokens can be redeemed to claim a proportional amount of the overflow. For example, a wallet with 1% of the total token supply can redeem those tokens to claim 1% of the overflow.

If _Allow minting tokens_ is enabled, the project owner can mint any amount of tokens to any wallet. Once a project is created, the project owner can use Juicebox to issue ERC-20 tokens for their project. Future payers will receive ERC-20 tokens, and existing token holders can claim ERC-20 tokens based off of existing token balances.

These tokens can then be used to connect with services such as [Snapshot](https://snapshot.org/#/) for off-chain voting, or any other service that is compatible with the ERC-20 standard.

### Reserved Tokens

The **Reserved Tokens** section determines what percentage of tokens goes to the payer and what percentage of tokens goes to a designated list of Ethereum wallets and Juicebox projects.

* _Reserved tokens_ determines what percentage of token issuance is reserved.
* _Allocate reserved tokens_ determines how reserved tokens will be distributed. By default, reserved tokens go to the project owner.

### Incentives

Juicebox projects can enable a _Discount rate_ and _Bonding curve rate._

* _Discount rate_ is a percentage that changes the cost of issuing tokens over time. Each funding cycle, the amount of tokens issued per ETH will decrease by the discount rate. Generally, this will reward people who fund your project earlier.
* _Bonding curve rate_ changes the amount of overflow that each token can be redeemed for. A Bonding curve rate of 60% means that tokens can be redeemed for 60% of the value they correspond to. The other 40% remains in the treasury, increasing the value of other tokens and rewarding long-term token holders.

{% embed url="https://youtu.be/dxqc3yMqi5M" %}
Bonding Curve 101
{% endembed %}

