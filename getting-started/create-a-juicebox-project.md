---
description: A step by step guide to kickstart your project.
---

# Create a Juicebox Project

{% hint style="warning" %}
The Juicebox protocol is not formally audited. All projects built on it may be vulnerable to bugs or exploits. Be smart and be curious, ask questions if you've got any.
{% endhint %}

Welcome folks!

To create a Juicebox Project, first press the 'connect' button in the top right corner of the [homepage](http://juicebox.money) to connect your wallet to Juicebox.

Next, click on 'Design your Project' or scroll down the homepage. The project design wizard is divided into two parts: the left side shows you all the necessary configuration steps, and the right side previews what your project will look like once created.

{% hint style="info" %}
Consider creating a test project on the Rinkeby testnet version of Juicebox at [rinkeby.juicebox.money](http://rinkeby.juicebox.money) before creating project on Ethereum mainnet. To use the Rinkeby version of Juicebox, you'll have to change your wallet to the Rinkeby network.
{% endhint %}

## Video Tutorial

This video tutorial gives an overview of setting up a Juicebox project.

{% embed url="https://youtu.be/I8euJjI5x1M" %}

## Section 1 - Identity

Complete as much as possible of the Identity Section of the project. Later changes to this field will cost gas.

## Section 2 - Funding

The Funding Section consists of picking a funding target and a funding cycle duration.

- **Funding Targets** are financial goals set for your project. The money invested into your project is placed into your treasury and creates tokens. If at any point the balance of the treasury is larger than the target funding you have set, then the access is called overflow. Anyone that has tokens can redeem them for a % of the overflow based on the bonding curve (check out Section 6 - Incentives for more info on the bonding curve). Having a Funding Target is required in order to have an Incentives Section.
- The **Funding Period** lets you lock in and automate your budget parameters over time, and determines how often you can withdraw your target amount. Not having a Funding Period allows you to withdraw money from the project anytime, and allows you to manually trigger new funding cycles whenever you want.

  Your treasury strategy (including contributor payouts and fixed monthly costs) should be considered when creating a funding target and funding period.

## 第 3 节 - 资金分配

在资金分配部分,你可以决定如何分配募集到的资金。资金接收者可以是任意的以太坊地址 或 任何其他 Juicebox 项目. 如果你将资金发送至一个 Juicebox 项目，则需要指明最终获得该项目代币的地址。你可以通过设定一个锁定日期来对资金支付进行管理,在锁定日期到达前或者筹资阶段被重新配置前,资金的支付去向无法被改变或删除。

- 在该节中所设定的百分比是相对于你所设定的筹资目标而言的。
- 当你从项目资金池中取出筹资目标以内的资金时, 被取出的资金将被分配至预先设定好的资金接收者的地址中.

## 第 4 节 - 预留代币

预留代币是指每次项目在收到筹款并进行代币铸造时,会有一部分的代币被保留并发送到预先设置好的地址中. 在这一节中, 你可以设置预留代币的百分比数以及这些代币在预留地址中如何分配.

- 例如, 当你的项目收到 1 个 ETH 时, 它会铸造 100 个项目代币. 如果预留代币被设置为 10%, 预先设置好的地址中包含两位开发人员的地址, 并且每人收到的代币数量为 50%.则付款人会收到 90 个项目代币,每位开发人员得到 5 个项目代币.

## 第 5 节 - 重新配置

在 "重新配置 "部分中，你可以设置对一个筹资周期进行更改前所需的时间。

- 选项 1 为七天的延迟，这意味着当有提案要对下一个筹资周期及进行更改时,必须在其生效前七天提交。这可以使提案清晰化，并让社区有充分的时间对提案进行分析。
- 选项 2 为不对筹资周期的修改进行任何延迟, 所有的改变都将被立即实施。这可能是具有风险的，因为对筹资周期更改的立即生效会允许很多危险操作，例如洗劫项目资金池。
- 选项 3 为创建一个可定制的合同，你可以为筹资周期的更改设定自己的规则。

## 第 6 节 - 代币铸造和赎回

这一部分只有在资金目标不为 0 时才可以进行设置。在这一节中,你可以通过设置参数来使项目的早期支持者以及长期项目代币持有人获得一定的优势。这些参数为：折扣率（DR）和联合曲线（BC）。

- 折扣率（DR）定义了每一个 ETH 可铸造的项目代币数量的减少速度。如果 1 枚 ETH 在第一个筹资周期内可以铸造 100 枚代币，并且 DR 为 20%，那么 1 枚 ETH 在第二个筹资周期中只能铸造 80 枚代币。当 DR 保持不变时，在第三个筹款周期中, 1 枚 ETH 将铸造 64 枚项目代币。这个复合系统总是在下一个筹资周期的价值上应用设定的 DR 百分比数。并且折扣率是不可逆的，所以需要根据项目进度来仔细设置。这一参数的主要目的是为了支持和激励那些承担了更多风险的早期支持者。在 0%的折扣率下，即使在不同的筹资周期内, 1 枚 ETH 可铸造的项目代笔数量是相同的. 目前可以设置的的最大折扣率为 20%。

- 联合曲线（BC）定义了当代币持有者通过燃烧项目代币从溢出资金中赎回资金时, 他们可以得到的 ETH 数量。当 BC 为 100%时，如果一位代币持有人拥有 1%的项目代币, 则他可以通过燃烧这 1%项目代币来获得溢出资金中 1%的 ETH. BC 越低时，代币持有者可以从溢出资金中赎回的资金越少. 未被赎回的溢出资金会被其他代币持有人所分享。BC 的机制奖励了那些长期项目代币持有者，但请注意，当项目方通过 Juicebox 将资金分配出去后，代币的可兑换价值也会减少。你可以在下一期筹资阶段中重新设置项目的联合曲线.。
