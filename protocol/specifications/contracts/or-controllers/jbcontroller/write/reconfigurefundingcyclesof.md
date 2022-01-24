# reconfigureFundingCyclesOf

Contract: [`JBController`](../)​‌

Interface: [`IJBController`](../../../../interfaces/ijbcontroller.md)

{% tabs %}
{% tab title="Step by step" %}
**Configures the properties of the current funding cycle if the project hasn't distributed tokens yet, or sets the properties of the proposed funding cycle that will take effect once the current one expires if it is approved by the current funding cycle's ballot.**

_Only a project's owner or a designated operator can configure its funding cycles._

## Definition

```solidity
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256) { ... }
```

* Arguments:
  * `_projectId` is the ID of the project whose funding cycles are being reconfigured.
  * `_data` is a [`JBFundingCycleData`](../../../../data-structures/jbfundingcycledata.md) data structure that defines the project's funding cycle that will be queued. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a [`JBFundingCycleMetadata`](../../../../data-structures/jbfundingcyclemetadata.md) data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
  * `_mustStartAtOrAfter` is the time before which the configured funding cycle can't start.
  * `_groupedSplits` is an array of [`JBGroupedSplits`](../../../../data-structures/jbgroupedsplits.md) data structures containing splits to set for any number of groups.
  * `_fundAccessConstraints` is an array of [`JBFundAccessConstraints`](../../../../data-structures/jbfundaccessconstraints.md) data structures containing amounts that a project can distribute during each funding cycle and amounts that can be used from its own overflow on-demand for each payment terminal. The `distributionLimit` applies for each funding cycle, and the `overflowAllowance` applies for the entirety of the configuration.
* Through the [`requirePermission`](../../../or-abstract/jboperatable/modifiers/requirepermission.md) modifier, the function is only accessible by the project's owner, or from an operator that has been given the `JBOperations.RECONFIGURE` permission by the project owner for the provided `_projectId`.
* The function returns the configuration of the funding cycle that was successfully updated.

## Body

1.  Configure the project's funding cycle, fund access constraints, and splits.

    ```solidity
    return _configure(_projectId, _data, _metadata, _groupedSplits, _fundAccessConstraints);
    ```

    _Internal references:_

    * [`_configure`](\_configure.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Configures the properties of the current funding cycle if the project hasn't distributed tokens yet, or
  sets the properties of the proposed funding cycle that will take effect once the current one expires
  if it is approved by the current funding cycle's ballot.

  @dev
  Only a project's owner or a designated operator can configure its funding cycles.

  @param _projectId The ID of the project whose funding cycles are being reconfigured.
  @param _data A JBFundingCycleData data structure that defines the project's funding cycle that will be queued. These properties will remain fixed for the duration of the funding cycle.
  @param _metadata A JBFundingCycleMetadata data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
  @param _mustStartAtOrAfter The time before which the configured funding cycle can't start.
  @param _groupedSplits An array of splits to set for any number of group.
  @param _fundAccessConstraints An array containing amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal.

  @return The configuration of the funding cycle that was successfully reconfigured.
*/
function reconfigureFundingCyclesOf(
  uint256 _projectId,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints
)
  external
  requirePermission(projects.ownerOf(_projectId), _projectId, JBOperations.RECONFIGURE)
  returns (uint256)
{
  return
    _configure(
      _projectId,
      _data,
      _metadata,
      _mustStartAtOrAfter,
      _groupedSplits,
      _fundAccessConstraints
    );
}
```
{% endtab %}

{% tab title="Errors" %}
| String                               | Description                                                |
| ------------------------------------ | ---------------------------------------------------------- |
| **`INVALID_RESERVED_RATE`**          | Thrown if the reserved rate is greater than 100%.          |
| **`INVALID_REDEMPTION_RATE`**        | Thrown if the redemption rate is greater than 100%.        |
| **`INVALID_BALLOT_REDEMPTION_RATE`** | Thrown if the ballot redemption rate is greater than 100%. |
{% endtab %}

{% tab title="Events" %}
| Name                                                                    | Data                                                                                                                                                                                                                                                                                                                                                      |
| ----------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**`SetFundAccessConstraints`**](../events/setfundaccessconstraints.md) | <ul><li><code>uint256 indexed fundingCycleConfiguration</code></li><li><code>uint256 indexed fundingCycleNumber</code></li><li><code>uint256 indexed projectId</code></li><li><a href="../../../../data-structures/jbfundaccessconstraints.md"><code>JBFundAccessConstraints</code></a><code>constraints</code></li><li><code>address caller</code></li></ul> |
{% endtab %}

{% tab title="Bug bounty" %}
| Category          | Description                                                                                                                            | Reward |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| **Optimization**  | Help make this operation more efficient.                                                                                               | 0.5ETH |
| **Low severity**  | Identify a vulnerability in this operation that could lead to an inconvenience for a user of the protocol or for a protocol developer. | 1ETH   |
| **High severity** | Identify a vulnerability in this operation that could lead to data corruption or loss of funds.                                        | 5+ETH  |
{% endtab %}
{% endtabs %}
