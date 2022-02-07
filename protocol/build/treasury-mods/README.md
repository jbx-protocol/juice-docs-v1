# Treasury mods

Treasury mods allow projects to override or extend the default Juicebox protocol functionality with custom contract logic.

|                           |                                                                                                                                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Funding cycle data source | Projects can attach a data source contract address to a funding cycle configuration to provide custom data that should be used when processing a payment or a redemption.                         |
| Pay delegate              | Projects can return a pay delegate contract address from its data source that will be called when it receives a payment.                                                                          |
| Redemption delegate       | Projects can return a redemption delegate contract address from its data source that will be called when its token holders redeem.                                                                |
| Funding cycle ballot      | Projects can attach a ballot contract address to a funding cycle configuration to provide certain conditions according to which subsequent reconfigurations must adhere in order to take effect.  |
| Split allocator           | Projects can route payouts from its treasury or reserved tokens to an allocator contract that will be called upon distribution.                                                                   |

