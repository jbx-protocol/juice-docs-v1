# Project

### What you need to know

* Each project within the Juicebox protocol is represented as an ERC-721 NFT.
* Whoever is the owner of a project's NFT has access to [admin functionality](project.md#project-admin) for that project within the protocol, which ultimately gives it control over the project's funds.
* Projects can be created either within the context of Juicebox with a call to [`JBController.launchProjectFor(...)`](../../specifications/contracts/or-controllers/jbcontroller/write/launchprojectfor.md) which also configures its funding cycle properties and sets it up to receive payments, or by itself with a call to [`JBProjects.createFor(...)`](../../specifications/contracts/jbprojects/write/createfor.md). The `launchProjectFor` transaction calls `createFor` as part of its routine.

### Admin functionality

For each project, the following functions can only be accessed by either the address that owns the project's NFT or by operator addresses explicitly allowed by the address that owns the project's NFT. Operators are only valid in the context of a particular owner – if the NFT changes hands, the operators for the project must be set again by the new owner.

* [`JBController.reconfigureFundingCyclesOf(...)`](../../specifications/contracts/or-controllers/jbcontroller/write/reconfigurefundingcyclesof.md)
* [`JBController.mintTokensOf(...)`](../../specifications/contracts/or-controllers/jbcontroller/write/minttokensof.md)
* [`JBController.migrate(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/migrate.md)
* [`JBETHPaymentTerminal.useAllowanceOf(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/useallowanceof.md)
* [`JBETHPaymentTerminal.migrate(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/migrate.md)
* [`JBETHPaymentTerminal.processFees(...)`](../../specifications/contracts/or-payment-terminals/jbethpaymentterminal/write/processfees.md)
* [`JBProjects.setHandleOf(...)`](../../specifications/contracts/jbprojects/write/sethandleof.md)
* [`JBProjects.setMetadataCidOf(...)`](../../specifications/contracts/jbprojects/write/setmetadatacidof.md)
* [`JBProjects.transferHandleOf(...)`](../../specifications/contracts/jbprojects/write/transferhandleof.md)
* [`JBProjects.renewHandleOf(...)`](../../specifications/contracts/jbprojects/write/renewhandleof.md)
* [`JBSplitStore.set(...)`](../../specifications/contracts/jbsplitstore/write/set.md)
* [`JBTokenStore.issueFor(...)`](../../specifications/contracts/jbtokenstore/write/issuefor.md)
* [`JBTokenStore.changeTokenOf(...)`](../../specifications/contracts/jbtokenstore/write/changetokenof.md)
* [`JBTokenStore.shouldRequireClaimingFor(...)`](../../specifications/contracts/jbtokenstore/write/shouldrequireclaimingfor.md)
* [`JBDirectory.setControllerOf(...)`](../../specifications/contracts/jbdirectory/write/setcontrollerof.md)
* [`JBDirectory.addTerminalOf(...)`](../../specifications/contracts/jbdirectory/write/addterminalof.md)
* [`JBDirectory.removeTerminalOf(...)`](../../specifications/contracts/jbdirectory/write/removeterminalof.md)
* [`JBDirectory.setPrimaryTerminalOf(...)`](../../specifications/contracts/jbdirectory/write/setprimaryterminalof.md)
