# Tickets

## Constructor

```text
constructor(string memory _name, string memory _symbol)
    ERC20(_name, _symbol)
    ERC20Permit(_name)
```

## Write

```text
function print(address _account, uint256 _amount)
    external
    override
    onlyOwner
{
    return _mint(_account, _amount);
}
```

```text
function redeem(address _account, uint256 _amount)
    external
    override
    onlyOwner
{
    return _burn(_account, _amount);
}
```

