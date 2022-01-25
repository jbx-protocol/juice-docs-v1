# Project

#### What everyone needs to know

* Each project within the Juicebox protocol is represented as an ERC-721 NFT.
* Whoever is the owner of a project's NFT has access to [admin functionality](project.md#admin-functionality) for that project within the protocol, which ultimately gives it control over the project's funds.

#### What you'll want to know if you're building

* Projects can be created either within the context of Juicebox with a call to [`JBController.launchProjectFor(...)`](../../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) which also configures its funding cycle properties and sets it up to receive payments, or by itself with a call to [`JBProjects.createFor(...)`](../../specifications/contracts/jbprojects/write/createfor.md). The `launchProjectFor` transaction calls `createFor` as part of its routine.
* A project can accomodate arbitrary metadata for any number of domains that can be updated by the project owner at any time using the [`JBProject.setMetadataOf(...)`](../../specifications/contracts/jbprojects/write/setmetadataof.md) transaction. This can be used by clients to store a reference to metadata stored on IPFS, or whatever. The protocol does not define standards for this metadata. A project's current metadata for any particular domain can be found by reading from [`JBProject.metadataContentOf(...)`](../../specifications/contracts/jbprojects/properties/metadatacontentof.md).
* Look through the [`JBProjects`](../../specifications/contracts/jbprojects/) contract for a complete list of relevant read functions, write functions, and emitted events.

## Admin functionality

For each project, the following functions can only be accessed by either the address that owns the project's NFT or by operator addresses explicitly allowed by the address that owns the project's NFT. Operators are only valid in the context of a particular owner – if the NFT changes hands, the operators for the project must be set again by the new owner.

An address can set operators for its project through the [`JBOperatorStore.setOperator(..`](../../specifications/contracts/jboperatorstore/write/setoperator.md)`.)` transaction, using the indexes from the [`JBOperations`](../../specifications/libraries/jboperations.md) library. The specific permissions a particular Operator is allowed depend on the specific parameters the admin allows them. Each of the following functions can be called by the admin, and also by any operator that has been granted permission to call the function by the admin.

* [`JBController.reconfigureFundingCyclesOf(...)`](../../specifications/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md)
* [`JBController.mintTokensOf(...)`](../../specifications/contracts/or-controllers/jbcontroller/write/minttokensof.md)
* [`JBController.migrate(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/migrate.md)
* [`JBETHPaymentTerminal.useAllowanceOf(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/useallowanceof.md)
* [`JBETHPaymentTerminal.migrate(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/migrate.md)
* [`JBETHPaymentTerminal.processFees(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/processfees.md)
* [`JBProjects.setHandleOf(...)`](../../specifications/contracts/jbprojects/write/sethandleof.md)
* [`JBProjects.setMetadataOf(...)`](../../specifications/contracts/jbprojects/write/setmetadataof.md)
* [`JBSplitsStore.set(...)`](../../specifications/contracts/jbsplitsstore/write/set.md)
* [`JBTokenStore.issueFor(...)`](../../specifications/contracts/jbtokenstore/write/issuefor.md)
* [`JBTokenStore.changeTokenOf(...)`](broken-reference/)
* [`JBTokenStore.shouldRequireClaimingFor(...)`](../../specifications/contracts/jbtokenstore/write/shouldrequireclaimingfor.md)
* [`JBDirectory.setControllerOf(...)`](../../specifications/contracts/jbdirectory/write/setcontrollerof.md)
* [`JBDirectory.addTerminalsOf(...)`](../../specifications/contracts/jbdirectory/write/addterminalsof.md)
* [`JBDirectory.removeTerminalOf(...)`](../../specifications/contracts/jbdirectory/write/removeterminalof.md)
* [`JBDirectory.setPrimaryTerminalOf(...)`](../../specifications/contracts/jbdirectory/write/setprimaryterminalof.md)
