# Operator

#### What everyone needs to know

* An operator is an address that has been given permission to take one or more actions on another address's behalf.
* Several functions throughout the Juicebox ecosystem that impact a project are available only to the a project's owner, or to any operator address that the project's owner has set.
* Operator permissions are stored and managed in the [`JBOperatorStore`](../../specifications/contracts/jboperatorstore/), where they can be added or revoked at any time by the address being operated on behalf of.
* See the [Projects topic](project.md) for more info on admin actions that are operatable.
* Operator permissions are expressed in terms of indexes defined in [`JBOperations`](../../specifications/libraries/jboperations.md).
* Operator permissions apply to a specific domain, which is used in the Juicebox ecosystem to allow addresses to give permissions that only apply to a specific project (where the domain is the projectId). A domain of 0 is a wildcard domain.

#### What you'll want to know if you're building

* All permission indexes can be found in [`JBOperations`](../../specifications/libraries/jboperations.md).
* Any address can give an operator permissions to take one or more actions on its behalf by sending a transaction to [`JBOperatorStore.setOperator(...)`](../../specifications/contracts/jboperatorstore/events/setoperator.md). To set multiple operators in the same transaction, use [`JBOperatorStore.setOperators(...)`](../../specifications/contracts/jboperatorstore/write/setoperators.md).
* Access can be revoked from an operator through the same operations as above by sending  an array of permissions that does not include those you wish to revoke.
* Permission for each operation is stored in a bit within an `uint256`. If the bit is 1, the permission is enabled for the particular operator within the particular domain. Otherwise it is disabled.&#x20;
* [`JBOperatorStore.hasPermission(...)`](../../specifications/contracts/jboperatorstore/read/haspermission.md) and [`JBOperatorStore.hasPermissions(...)`](../../specifications/contracts/jboperatorstore/read/haspermissions.md)can be used to check if an operator has a particular permission.
