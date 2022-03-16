# launchProjectFor

Contract: [`JBController`](../)​‌

{% tabs %}
{% tab title="Step by step" %}
**Creates a project. This will mint an ERC-721 into the specified owner's account, configure a first funding cycle, and set up any splits.**

_Each operation within this transaction can be done in sequence separately._

_Anyone can deploy a project on an owner's behalf._

#### Definition

```solidity
function launchProjectFor(
  address _owner,
  JBProjectMetadata calldata _projectMetadata,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) { ... }
```

* Arguments:
  * `_owner` is the address to set as the owner of the project. The project ERC-721 will be owned by this address.
  * `_projectMetadata` is a link to associate with the project within a particular domain. This can be updated any time by the owner of the project.
  * `_data` is a [`JBFundingCycleData`](../../../../data-structures/jbfundingcycledata.md) data structure that defines the project's first funding cycle. These properties will remain fixed for the duration of the funding cycle.
  * `_metadata` is a [`JBFundingCycleMetadata`](../../../../data-structures/jbfundingcyclemetadata.md) data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
  * `_mustStartAtOrAfter` is the time before which the configured funding cycle can't start.
  * `_groupedSplits` is an array of [`JBGroupedSplits`](../../../../data-structures/jbgroupedsplits.md) data structures containing splits to set for any number of groups. The core protocol makes use of groups defined in [`JBSplitsGroups`](../../../../libraries/jbsplitsgroups.md).
  * `_fundAccessConstraints` is an array of [`JBFundAccessConstraints`](../../../../data-structures/jbfundaccessconstraints.md) data structures containing amounts that a project can distribute during each funding cycle and amounts that can be used from its own overflow on-demand for each payment terminal. The `distributionLimit` applies for each funding cycle, and the `overflowAllowance` applies for the entirety of the configuration.
  * `_terminals` is an array of [`IJBTerminal`](../../../../interfaces/ijbterminal.md) payment terminals to add for the project.
* The function can be accessed externally by anyone.
* The function returns the ID of the project that was launched.

#### Body

1.  Create the project. This will mint an ERC-721 in the `_owners` wallet representing ownership over the project.

    ```solidity
    // Mint the project into the wallet of the message sender.
    projectId = projects.createFor(_owner, _projectMetadata);
    ```

    _External references:_

    * [`createFor`](../../../jbprojects/write/createfor.md)
2.  Set this controller as the controller of the project.

    ```solidity
    // Set this contract as the project's controller in the directory.
    directory.setControllerOf(projectId, this);
    ```

    _External references:_

    * [`setControllerOf`](../../../jbdirectory/write/setcontrollerof.md)
3.  Configure the project's funding cycle, fund access constraints, and splits.

    ```solidity
    _configure(
      projectId,
      _data,
      _metadata,
      _mustStartAtOrAfter,
      _groupedSplits,
      _fundAccessConstraints
    );
    ```

    _Internal references:_

    * [`_configure`](\_configure.md)
4.  If terminals were provided, add them to the list of terminals the project can accept funds through.

    ```solidity
    // Add the provided terminals to the list of terminals.
    if (_terminals.length > 0) directory.addTerminalsOf(projectId, _terminals);
    ```

    _External references:_

    * [`addTerminalsOf`](../../../jbdirectory/write/addterminalsof.md)
{% endtab %}

{% tab title="Code" %}
```solidity
/**
  @notice
  Creates a project. This will mint an ERC-721 into the specified owner's account, configure a first funding cycle, and set up any splits.

  @dev
  Each operation within this transaction can be done in sequence separately.

  @dev
  Anyone can deploy a project on an owner's behalf.

  @param _owner The address to set as the owner of the project. The project ERC-721 will be owned by this address.
  @param _projectMetadata A link to associate with the project within a particular domain. This can be updated any time by the owner of the project.
  @param _data A JBFundingCycleData data structure that defines the project's first funding cycle. These properties will remain fixed for the duration of the funding cycle.
  @param _metadata A JBFundingCycleMetadata data structure specifying the controller specific params that a funding cycle can have. These properties will remain fixed for the duration of the funding cycle.
  @param _mustStartAtOrAfter The time before which the configured funding cycle can't start.
  @param _groupedSplits An array of splits to set for any number of group. The core protocol makes use of groups defined in `JBSplitsGroups`.
  @param _fundAccessConstraints An array containing amounts, in wei (18 decimals), that a project can use from its own overflow on-demand for each payment terminal.
  @param _terminals Payment terminals to add for the project.

  @return projectId The ID of the project.
*/
function launchProjectFor(
  address _owner,
  JBProjectMetadata calldata _projectMetadata,
  JBFundingCycleData calldata _data,
  JBFundingCycleMetadata calldata _metadata,
  uint256 _mustStartAtOrAfter,
  JBGroupedSplits[] memory _groupedSplits,
  JBFundAccessConstraints[] memory _fundAccessConstraints,
  IJBTerminal[] memory _terminals
) external returns (uint256 projectId) {
  // Mint the project into the wallet of the message sender.
  projectId = projects.createFor(_owner, _projectMetadata);

  // Set this contract as the project's controller in the directory.
  directory.setControllerOf(projectId, this);

  _configure(
    projectId,
    _data,
    _metadata,
    _mustStartAtOrAfter,
    _groupedSplits,
    _fundAccessConstraints
  );

  // Add the provided terminals to the list of terminals.
  if (_terminals.length > 0) directory.addTerminalsOf(projectId, _terminals);
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
| Name                                                                    | Data                                                                                                                                                                                                                                                                                                                                                          |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
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
