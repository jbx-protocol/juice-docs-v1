# IJBFundingCycleDataSource

```solidity
interface IJBFundingCycleDataSource {
  function payParams(JBPayParamsData calldata _param)
    external
    view
    returns (
      uint256 weight,
      string memory memo,
      IJBPayDelegate delegate,
      bytes memory delegateMetadata
    );

  function redeemParams(JBRedeemParamsData calldata _param)
    external
    view
    returns (
      uint256 amount,
      string memory memo,
      IJBRedemptionDelegate delegate,
      bytes memory delegateMetadata
    );
}l
```
