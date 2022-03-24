# Learn | Tokens

#### What everyone needs to know

* By default, all payments that come in to a Juicebox project mint tokens. These tokens are distributed to the contributor of the payment, as well as to any addresses specified in the project's reserved token list. The amount of tokens minted depends on the amount paid and the `weight` of the project's current funding cycle. Projects can override or extended this default behavior.
* By default, the protocol allocates tokens to recipients using an internal accounting mechanism.
* Projects can issue their own ERC-20 token directly from the protocol to use as its token. Projects can also bring their own token as long as it conforms to the [`IJBToken`](../../api/interfaces/ijbtoken.md) interface. This makes it possible to use ERC-1155's, or custom tokens.
* Once the project has issued a token, anyone can claim tokens, which exports them from the protocol's internal accounting mechanism into the holder's wallet to use across Web3. A project's owner can also force all of the project's tokens to be issued directly into the exported version. This bypasses the internal accounting mechanism, but slightly increases gas for each transaction that requires tokens to be minted.
* By default, tokens can be redeemed (burned) by holders to claim a portion of what's in the project's overflow. The amount of overflow claimable is determined by [`redemptionRate`](redemption-rate.md) of the project's current funding cycle. Projects can override or extend this default behavior.
* A project owner can mint and distribute more of the project's tokens on demand. This behavior can be paused on a per-funding cycle basis.
* Tokens can be [redeemed](redemption-rate.md) for [overflow](overflow.md).
* A project can use its tokens however it wishes.

#### What you'll want to know if you're building

* Tokens can be minted on-demand by calling [`JBController.mintTokensOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/minttokensof.md). The ability to do so can be turned off via a funding cycle configuration metadata parameter.
* Tokens can be burned on-demand by called [`JBController.burnTokensOf(...)`](../../api/contracts/or-controllers/jbcontroller/write/burntokensof.md). The ability to do so can be turned off via a funding cycle configuration metadata parameter.
