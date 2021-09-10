# createFor



{% tabs %}
{% tab title="Step by step" %}
The function has the following definition:

```javascript
function createFor(
    address _owner,
    bytes32 _handle,
    string calldata _uri,
    IJBTerminal _terminal
) external override returns (uint256);
```

* `_owner` is the address that will be the owner of the project.
* `_handle` is a unique string to associate with the project that will resolve to its token ID.
* `_uri` is an IPFS CID hash where metadata about the project has been uploaded. An empty string is acceptable if no metadata is being provided.
* `_terminal` is the terminal contract that should be set as the project's current payment terminal, through which is will be able to manage funds. The zero address is acceptable if the project should not yet be associated with a terminal.
* The function can be accessed externally by anyone. 
* The function overrides a function definition from the `IJBProjects` interface.
* Returns the token ID of the newly created project.

1. Check that the provided `_handle` is not empty.

```javascript
    // Handle must exist.
    require(_handle != bytes32(0), "JBProjects::createFor: EMPTY_HANDLE");
```

2. Check that the `_handle` is unique. This is done by making sure the handle isn't currently mapped to a project, and isn't currently being transferred.

```javascript
// Handle must be unique.
require(
    projectFor[_handle] == 0 &&
        transferAddressFor[_handle] == address(0),
    "JBProjects::createFor: HANDLE_TAKEN"
);
```

3. Increment the count to include the new project being created. 

```javascript
// Increment the count, which will be used as the ID.
count++;
```

4. Mint a new NFT token belonging to the `_owner` using the `count` as the token ID. 

```javascript
// Mint the project.
_safeMint(_owner, count);
```

5. Store the provided `_handle` as the as the `handleOf` the newly created project.

```javascript
// Set the handle stored values.
handleOf[count] = _handle;
```

6. Store the newly created project's ID as the `projectFor` the provided `_handle` to allow for project lookup using the handle.

```javascript
  idFor[_handle] = count;
```

7. If a URI was provided,  store it as the `uriOf` the newly created project. 

```javascript
// Set the URI if one was provided.
if (bytes(_uri).length > 0) uriOf[count] = _uri;
```

8. If a terminal address was provided, set it as the terminal of the newly created project in the directory of the terminal. 

This will let other people/contracts around Web3 know where to send funds to your project, and will also let the `TokenStore` and `FundingCycleStore` know which terminal contract has the authority to manipulate data pertaining to the project.

```javascript
// Set the project's terminal if needed.
if (_terminal != IJBTerminal(address(0)))
    _terminal.directory().setTerminalOf(count, _terminal);
```

9. Emit a [`Create`](../events/create.md) event that records the fact that this transaction happened with the given parameters. 

```
emit Create(count, _owner, _handle, _uri, _terminal, msg.sender);
```

10. Return the newly created project's token ID.

```javascript
 return count;
```
{% endtab %}

{% tab title="Only code" %}
```javascript
/**
    @notice 
    Create a new project for the specified owner.

    @dev 
    Anyone can create a project on an owner's behalf.

    @param _owner The address that will be the owner of the project.
    @param _handle A unique string to associate with the project that will resolve to its token ID.
    @param _uri An IPFS CID hash where metadata about the project has been uploaded. An empty string is acceptable if no metadata is being provided.
    @param _terminal The terminal contract that should be set as the project's current payment terminal, through which is will be able to manage funds. The zero address is acceptable if the project should not yet be associated with a terminal.

    @return The token ID of the newly created project.
*/
function createFor(
    address _owner,
    bytes32 _handle,
    string calldata _uri,
    IJBTerminal _terminal
) external override returns (uint256) {
    // Handle must exist.
    require(_handle != bytes32(0), "JBProjects::createFor: EMPTY_HANDLE");

    // Handle must be unique.
    require(
        projectFor[_handle] == 0 &&
            transferAddressFor[_handle] == address(0),
        "JBProjects::createFor: HANDLE_TAKEN"
    );

    // Increment the count, which will be used as the ID.
    count++;

    // Mint the project.
    _safeMint(_owner, count);

    // Set the handle stored values.
    handleOf[count] = _handle;
    idFor[_handle] = count;

    // Set the URI if one was provided.
    if (bytes(_uri).length > 0) uriOf[count] = _uri;

    // Set the project's terminal if needed.
    if (_terminal != IJBTerminal(address(0)))
        _terminal.directory().setTerminalOf(count, _terminal);

    emit Create(count, _owner, _handle, _uri, _terminal, msg.sender);

    return count;
}
```
{% endtab %}

{% tab title="Errors" %}
| String | Description |
| :--- | :--- |
| **`JBProjects::createFor: EMPTY_HANDLE`** | Thrown if the provided handle is empty |
| **`JBProjects::createFor: HANDLE_TAKEN`** | Thrown if the provided handle is already in use. |
{% endtab %}

{% tab title="Events" %}
<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Data</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>Create</code></b>
      </td>
      <td style="text-align:left">
        <ul>
          <li><code>uint256 indexed projectId</code> 
          </li>
          <li><code>address indexed owner</code> 
          </li>
          <li><code>bytes32 indexed handle</code>
          </li>
          <li><code>string uri</code> 
          </li>
          <li><code>IJBTerminal terminal</code> 
          </li>
          <li><code>address caller</code>
          </li>
        </ul>
        <p><a href="../events/create.md">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>
{% endtab %}
{% endtabs %}





