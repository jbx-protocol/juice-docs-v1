# Operator

#### What everyone needs to know

* An operator is an address that has been given permission to take one or more actions on another address's behalf.
* Several functions throughout the Juicebox ecosystem that impact a project are available only to the a project's owner, or to any operator address that the project's owner has set.
* Operator permissions are stored and managed in the [`JBOperatorStore`](../../api/contracts/jboperatorstore/), where they can be added or revoked at any time by the address being operated on behalf of.
* See the [Projects topic](project.md) for more info on admin actions that are operatable.
* Operator permissions are expressed in terms of indexes defined in [`JBOperations`](../../api/libraries/jboperations.md).
* Operator permissions apply to a specific domain, which is used in the Juicebox ecosystem to allow addresses to give permissions that only apply to a specific project (where the domain is the projectId). A domain of 0 is a wildcard domain.

#### What you'll want to know if you're building

* All permission indexes can be found in [`JBOperations`](../../api/libraries/jboperations.md).
* Any address can give an operator permissions to take one or more actions on its behalf by sending a transaction to [`JBOperatorStore.setOperator(...)`](../../api/contracts/jboperatorstore/events/setoperator.md). To set multiple operators in the same transaction, use [`JBOperatorStore.setOperators(...)`](../../api/contracts/jboperatorstore/write/setoperators.md).
* Access can be revoked from an operator through the same operations as above by sending  an array of permissions that does not include those you wish to revoke.
* Permission for each operation is stored in a bit within an `uint256`. If the bit is 1, the permission is enabled for the particular operator within the particular domain. Otherwise it is disabled.&#x20;
* [`JBOperatorStore.hasPermission(...)`](../../api/contracts/jboperatorstore/read/haspermission.md) and [`JBOperatorStore.hasPermissions(...)`](../../api/contracts/jboperatorstore/read/haspermissions.md)can be used to check if an operator has a particular permission.

## Operatable functionality

For each project, the following functions can only be accessed by either the address that owns the project's NFT or by operator addresses explicitly allowed by the address that owns the project's NFT. Operators are only valid in the context of a particular owner – if the NFT changes hands, the operators for the project must be set again by the new owner.

An address can set operators for its project through the [`JBOperatorStore.setOperator(..`](../../api/contracts/jboperatorstore/write/setoperator.md)`.)` transaction, using the indexes from the [`JBOperations`](../../api/libraries/jboperations.md) library. The specific permissions a particular Operator is allowed depend on the specific parameters the admin allows them. Each of the following functions can be called by the admin, and also by any operator that has been granted permission to call the function by the admin.

* [`JBController.launchFundingCyclesOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/launchfundingcyclesof.md)
* [`JBController.reconfigureFundingCyclesOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md)
* [`JBController.mintTokensOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/minttokensof.md)
* [`JBController.issueTokenFor(...)`](../../api/contracts/or-controllers/jbcontroller/write/issuetokenfor.md)
* [`JBController.changeTokenOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/changetokenof.md)
* [`JBController.migrate(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/migrate.md)
* [`JBPayoutRedemptionPaymentTerminal.useAllowanceOf(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/useallowanceof.md)
* [`JBPayoutRedemptionPaymentTerminal.migrate(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/migrate.md)
* [`JBPayoutRedemptionPaymentTerminal.processFees(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/processfees.md)
* [`JBProjects.setMetadataOf(...)`](../../api/contracts/jbprojects/write/setmetadataof.md)
* [`JBSplitsStore.set(...)`](../../api/contracts/jbsplitsstore/write/set.md)
* [`JBTokenStore.shouldRequireClaimingFor(...)`](../../api/contracts/jbtokenstore/write/shouldrequireclaimingfor.md)
* [`JBDirectory.setControllerOf(...)`](../../api/contracts/jbdirectory/write/setcontrollerof.md)
* [`JBDirectory.addTerminalsOf(...)`](../../api/contracts/jbdirectory/write/addterminalsof.md)
* [`JBDirectory.removeTerminalOf(...)`](../../api/contracts/jbdirectory/write/removeterminalof.md)
* [`JBDirectory.setPrimaryTerminalOf(...)`](../../api/contracts/jbdirectory/write/setprimaryterminalof.md)

The following transactions can be reached by token holders, or by operator addresses explicitly allowed by the address that owns the tokens.  If the tokens change hands, the operators for the address must be set again by the new holder.

* [`JBController.burnTokensOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/burntokensof.md)
* [`JBPayoutRedemptionPaymentTerminal.redeemTokensOf(...)`](../../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/redeemtokensof.md)
* [`JBTokenStore.transferTo(...)`](../../api/contracts/jbtokenstore/write/transferto.md)