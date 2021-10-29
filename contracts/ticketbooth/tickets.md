# Tickets

## Constructor

```
constructor(string memory _name, string memory _symbol)
    ERC20(_name, _symbol)
    ERC20Permit(_name)
```

## Write

```
function print(address _account, uint256 _amount)
    external
    override
    onlyOwner
{
    return _mint(_account, _amount);
}
```

```
function redeem(address _account, uint256 _amount)
    external
    override
    onlyOwner
{
    return _burn(_account, _amount);
}
```
