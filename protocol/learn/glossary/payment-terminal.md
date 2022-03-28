# Payment terminal

#### What everyone needs to know

* A project can be configured to use any contract that adheres to [`IJBPaymentTerminal`](../../api/interfaces/ijbpaymentterminal.md) to manage its inflowns and outflows of token funds.
* Each terminal manages one token type only.
* Each payment terminal can have unique distribution limit and overflow allowance.
* Each payment terminal can behave differently when it receives payments.

#### What you'll want to know if you're building

* A project can set its terminals using [`JBDirectory.setTerminalsOf(...)`](../../api/contracts/jbdirectory/write/setterminalsof.md).
* If a project uses multiple tokens to manage funds for the same token, it can set the primary one where other Web3 contracts should send funds to using [`JBDirectory.setPrimaryTerminalOf(...)`](../../api/contracts/jbdirectory/write/setprimaryterminalof.md).
* To pay a project in a certain token, get it's prefered payment terminal using [`JBDirectory.primaryTerminalOf(...)`](../../api/contracts/jbdirectory/read/primaryterminalof.md). If no terminal is returned, the project is not currently accepting the specified token.
