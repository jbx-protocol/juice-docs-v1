# onlyAssociatedPaymentTerminal

```solidity
// A modifier only allowing the associated payment terminal to access the function.
modifier onlyAssociatedPaymentTerminal() {
  if (msg.sender != address(terminal)) {
      revert PAYMENT_TERMINAL_UNAUTHORIZED();
  }
  _;
}
```