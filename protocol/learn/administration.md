# Administration

The following functions can only be accessed by a privileged administrating address, initially the JuiceboxDAO multisig:

* **[`JBProjects.setTokenUriResolver(...)`](../api/contracts/jbprojects/write/settokenuriresolver.md)**\
  Allows the owner of the [`JBProjects`](../api/contracts/jbprojects/) contract to provide and change the [`IJBTokenUriResolver`](../api/interfaces/ijbtokenuriresolver.md) used to provide metadata for project NFTs in its [`tokenURI(...)`](../api/contracts/jbprojects/read/tokenuri.md) function.
  <br>
* **[`JBPrices.addFeedFor(...)`](../api/contracts/jbprices/write/addfeed.md)**\
  Allows the owner of the [`JBPrices`](../api/contracts/jbprices/) contract to add new price feeds used to convert amounts denoted in one currency to another. Once added, a price feed cannot be removed.
  <br>
* **[`JBDirectory.setIsAllowedToSetFirstController(...)`](../api/contracts/jbdirectory/write/setisallowedtosetfirstcontroller.md)**\
  Allows the owner of the [`JBDirectory`](../api/contracts/jbdirectory/) contract to add/remove addresses that can set a project's first controller on its behalf. 
  <br>
* **[`JBETHPaymentTerminal.setFee(...)`](../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/setfee.md)**\
  Allows the owner of the [`JBETHPaymentTerminal`](../api/contracts/or-payment-terminals/jbethpaymentterminal/) (or any other terminal inheriting from [`JBPayoutRedemptionPaymentTerminal`](../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/)) to change the protocol fee incurred when projects distribute their treasury funds outside of the protocol ecosystem. The max fee is 5%.
  <br>
* **[`JBETHPaymentTerminal.setFeeGauge(...)`](../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/setfeegauge.md)**\
  Allows the owner of the [`JBETHPaymentTerminal`](../api/contracts/or-payment-terminals/jbethpaymentterminal/) (or any other terminal inheriting from [`JBPayoutRedemptionPaymentTerminal`](../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/)) to change the fee gauge used to provide fee discounts on a per-project basis. 
  <br>
* **[`JBETHPaymentTerminal.setFeelessTerminal(...)`](../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/write/setfeelessterminal.md)**\
  Allows the owner of the [`JBETHPaymentTerminal`](../api/contracts/or-payment-terminals/jbethpaymentterminal/) (or any other terminal inheriting from [`JBPayoutRedemptionPaymentTerminal`](../api/contracts/or-abstract/jbpayoutredemptionpaymentterminal/)) to add/remove any other [`IJBTerminal`](../api/interfaces/ijbpaymentterminal.md) used by other projects from a list of terminals to which distributed funds can be sent without incurring protocol fees. 
  <br>

Ownership for each contract is managed independently.