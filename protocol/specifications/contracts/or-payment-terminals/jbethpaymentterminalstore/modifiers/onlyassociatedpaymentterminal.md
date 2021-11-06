# onlyAssociatedPaymentTerminal

```solidity
// A modifier only allowing the associated payment terminal to access the function.
modifier onlyAssociatedPaymentTerminal() {
  require(msg.sender == terminal, '0x3a: UNAUTHORIZED');
  _;
}
```