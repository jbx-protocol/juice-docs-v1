# IJBFundingCycleDataSource

```solidity
interface IJBFundingCycleDataSource {
  function payParams(JBPayParamsData calldata _param)
    external
    view
    returns (
      uint256 weight,
      string memory memo,
      IJBPayDelegate delegate
    );

  function redeemParams(JBRedeemParamsData calldata _param)
    external
    view
    returns (
      uint256 reclaimAmount,
      string memory memo,
      IJBRedemptionDelegate delegate
    );
}
```
