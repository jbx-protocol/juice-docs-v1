# IJBPayoutRedemptionPaymentTerminal

```solidity
interface IJBPayoutRedemptionPaymentTerminal is IJBPaymentTerminal {
  event AddToBalance(uint256 indexed projectId, uint256 amount, string memo, address caller);
  event Migrate(
    uint256 indexed projectId,
    IJBPaymentTerminal indexed to,
    uint256 amount,
    address caller
  );
  event DistributePayouts(
    uint256 indexed fundingCycleConfiguration,
    uint256 indexed fundingCycleNumber,
    uint256 indexed projectId,
    address beneficiary,
    uint256 amount,
    uint256 distributedAmount,
    uint256 feeAmount,
    uint256 beneficiaryDistributionAmount,
    string memo,
    address caller
  );

  event UseAllowance(
    uint256 indexed fundingCycleConfiguration,
    uint256 indexed fundingCycleNumber,
    uint256 indexed projectId,
    address beneficiary,
    uint256 amount,
    uint256 distributedAmount,
    uint256 feeAmount,
    string memo,
    address caller
  );
  event ProcessFees(uint256 indexed projectId, JBFee[] fees, address caller);
  event Pay(
    uint256 indexed fundingCycleConfiguration,
    uint256 indexed fundingCycleNumber,
    uint256 indexed projectId,
    address beneficiary,
    uint256 amount,
    uint256 beneficiaryTokenCount,
    string memo,
    address caller
  );
  event DelegateDidPay(IJBPayDelegate indexed delegate, JBDidPayData data, address caller);
  event RedeemTokens(
    uint256 indexed fundingCycleConfiguration,
    uint256 indexed fundingCycleNumber,
    uint256 indexed projectId,
    address holder,
    address beneficiary,
    uint256 tokenCount,
    uint256 claimedAmount,
    string memo,
    address caller
  );

  event DelegateDidRedeem(
    IJBRedemptionDelegate indexed delegate,
    JBDidRedeemData data,
    address caller
  );

  event DistributeToPayoutSplit(
    uint256 indexed fundingCycleConfiguration,
    uint256 indexed fundingCycleNumber,
    uint256 indexed projectId,
    JBSplit split,
    uint256 amount,
    address caller
  );

  event SetFee(uint256 fee, address caller);

  event SetFeeGauge(IJBFeeGauge feeGauge, address caller);

  event SetFeelessTerminal(IJBPaymentTerminal terminal, address caller);

  function projects() external view returns (IJBProjects);

  function splitsStore() external view returns (IJBSplitsStore);

  function directory() external view returns (IJBDirectory);

  function heldFeesOf(uint256 _projectId) external view returns (JBFee[] memory);

  function fee() external view returns (uint256);

  function feeGauge() external view returns (IJBFeeGauge);

  function isFeelessTerminal(IJBPaymentTerminal _terminal) external view returns (bool);

  function distributePayoutsOf(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedAmount,
    string calldata _memo
  ) external;

  function redeemTokensOf(
    address _holder,
    uint256 _projectId,
    uint256 _count,
    uint256 _minReturnedAmount,
    address payable _beneficiary,
    string calldata _memo,
    bytes calldata _metadata
  ) external returns (uint256 reclaimAmount);

  function useAllowanceOf(
    uint256 _projectId,
    uint256 _amount,
    uint256 _currency,
    uint256 _minReturnedAmount,
    address payable _beneficiary,
    string calldata _memo
  ) external;

  function migrate(uint256 _projectId, IJBPaymentTerminal _to) external;

  function processFees(uint256 _projectId) external;

  function setFee(uint256 _fee) external;

  function setFeeGauge(IJBFeeGauge _feeGauge) external;

  function toggleFeelessTerminal(IJBPaymentTerminal _terminal) external;
}
```