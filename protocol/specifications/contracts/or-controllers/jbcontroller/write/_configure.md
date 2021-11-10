# \_configure

{% tabs %}
{% tab title="Step by step" %}
**Configures a funding cycle and stores information pertinent to the configuration.**

# Definition

```solidity
function _configure(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
) private returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project whose funding cycles are being reconfigured.
  * `_data` is a [`JBFundingCycleData`](../../../../data-structures/jbfundingcycledata.md) data structure that defines the project's first funding cycle. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a [`JBFundingCycleMetadata`](../../../../data-structures/jbfundingcyclemetadata.md) data structure specifying the controller specific params that a funding cycle can have.
  * `_fundAccessConstraints` is an array of [`JBFundAccessConstraints`](../../../../data-structures/jbfundaccessconstraints.md) data structures containing amounts that a project can distribute during each funding cycle and amounts that can be used from its own overflow on-demand for each payment terminal.
  * `_groupedSplits` is an array of [`JBGroupedSplits`](../../../../data-structures/jbgroupedsplits.md) data structures containing splits to set for any number of groups.
* The function is private to this contract.
* The function returns the funding cycle configuration that was successfully updated.

# Body

1.  Configure the project's funding cycles. Pack the metadata into a `uint256`.

    ```solidity
    // Configure the funding cycle's properties.
    JBFundingCycle memory _fundingCycle = fundingCycleStore.configureFor(
      _projectId,
      _data,
      JBFundingCycleMetadataResolver.packFundingCycleMetadata(_metadata)
    );
    ```

    _External references:_

    * [`configureFor`](../../../jbfundingcyclestore/write/configurefor.md)

    _Libraries used:_

    * [`JBFundingCycleMetadataResolver`](../../../../libraries/jbfundingcyclemetadataresolver.md)\
      `.packFundingCycleMetadata(...)`

2.  For each provided group splits, set the splits for the specified group if there are any.

    ```solidity
    for (uint256 _i; _i < _groupedSplits.length; _i++)
      // Set payout splits if there are any.
      if (_groupedSplits[_i].splits.length > 0)
        splitsStore.set(
          _projectId,
          _fundingCycle.configuration,
          _groupedSplits[_i].group,
          _groupedSplits[_i].splits
        );
    ```

    _External references:_

    * [`set`](../../../jbsplitsstore/write/set.md)

3.  For each fund access constraint struct in the array passed in, store the values of the distribution limit, the overflow allowance, and the currency. Emit a `SetFundAccessConstraints` event with the relevant parameters.

    ```solidity
    // Set overflow allowances if there are any.
    for (uint256 _i; _i < _fundAccessConstraints.length; _i++) {
      JBFundAccessConstraints memory _constraints = _fundAccessConstraints[_i];

      // Set the distribution limit if there is one.
      if (_constraints.distributionLimit > 0)
        distributionLimitOf[_projectId][_fundingCycle.configuration][
          _constraints.terminal
        ] = _constraints.distributionLimit;

      // Set the overflow allowance if there is one.
      if (_constraints.overflowAllowance > 0)
        overflowAllowanceOf[_projectId][_fundingCycle.configuration][
          _constraints.terminal
        ] = _constraints.overflowAllowance;

      if (_constraints.currency > 0)
        currencyOf[_projectId][_fundingCycle.configuration][_constraints.terminal] = _constraints
          .currency;

      emit SetFundAccessConstraints(
        _fundingCycle.configuration,
        _fundingCycle.number,
        _projectId,
        _constraints,
        msg.sender
      );
    }
    ```

    _Internal references:_

    * [`distributionLimitOf`](../properties/distributionlimitof.md)
    * [`overflowAllowanceOf`](../properties/overflowallowanceof.md)
    * [`currencyOf`](../properties/currencyof.md)

    _Event references:_

    * [`SetFundAccessConstraints`](../events/setfundaccessconstraints.md)

4.  Return the funding cycle's configuration.

    ```solidity
    return _fundingCycle.configuration;
    ``

{% endtab %}

{% tab title="Only code" %}
```solidity
/** 
  @notice 
  Configures a funding cycle and stores information pertinent to the configuration.

  @dev
  See the docs for `launchProjectFor` and `reconfigureFundingCyclesOf`.
*/
function _configure(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
) private returns (uint256) {
  // Configure the funding cycle's properties.
  JBFundingCycle memory _fundingCycle = fundingCycleStore.configureFor(
    _projectId,
    _data,
    JBFundingCycleMetadataResolver.packFundingCycleMetadata(_metadata)
  );

  for (uint256 _i; _i < _groupedSplits.length; _i++)
    // Set splits for the current group being iterated on if there are any.
    if (_groupedSplits[_i].splits.length > 0)
      splitsStore.set(
        _projectId,
        _fundingCycle.configuration,
        _groupedSplits[_i].group,
        _groupedSplits[_i].splits
      );

  // Set overflow allowances if there are any.
  for (uint256 _i; _i < _fundAccessConstraints.length; _i++) {
    JBFundAccessConstraints memory _constraints = _fundAccessConstraints[_i];

    // Set the distribution limit if there is one.
    if (_constraints.distributionLimit > 0)
      distributionLimitOf[_projectId][_fundingCycle.configuration][
        _constraints.terminal
      ] = _constraints.distributionLimit;

    // Set the overflow allowance if there is one.
    if (_constraints.overflowAllowance > 0)
      overflowAllowanceOf[_projectId][_fundingCycle.configuration][
        _constraints.terminal
      ] = _constraints.overflowAllowance;

    if (_constraints.currency > 0)
      currencyOf[_projectId][_fundingCycle.configuration][_constraints.terminal] = _constraints
        .currency;

    emit SetFundAccessConstraints(
      _fundingCycle.configuration,
      _fundingCycle.number,
      _projectId,
      _constraints,
      msg.sender
    );
  }

  return _fundingCycle.id;
}
```
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
