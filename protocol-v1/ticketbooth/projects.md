# Projects

### Constructor

```javascript
/** 
  @param _operatorStore A contract storing operator assignments.
*/
constructor(IOperatorStore _operatorStore)
    ERC721("Juicebox project", "JUICEBOX PROJECT")
    Operatable(_operatorStore)
```

### Read

```javascript
/** 
  @notice 
  Whether the specified project exists.

  @param _projectId The project to check the existence of.

  @return A flag indicating if the project exists.
*/
function exists(uint256 _projectId) external view override returns (bool)
```

### Write

```javascript
/**
    @notice 
    Create a new project.

    @dev 
    Anyone can create a project on an owner's behalf.

    @param _owner The owner of the project.
    @param _handle A unique handle for the project.
    @param _uri An ipfs CID to more info about the project.
    @param _terminal The terminal to set for this project so that it can start receiving payments.

    @return The new project's ID.
*/
function create(
    address _owner,
    bytes32 _handle,
    string calldata _uri,
    ITerminal _terminal
) external override returns (uint256) 
```

```javascript
/**
  @notice 
  Allows a project owner to set the project's handle.

  @dev 
  Only a project's owner or operator can set its handle.

  @param _projectId The ID of the project.
  @param _handle The new unique handle for the project.
*/
function setHandle(uint256 _projectId, bytes32 _handle)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, Operations.SetHandle)
```

```javascript
/**
  @notice 
  Allows a project owner to set the project's uri.

  @dev 
  Only a project's owner or operator can set its uri.

  @param _projectId The ID of the project.
  @param _uri An ipfs CDN to more info about the project. Don't include the leading ipfs://
*/
function setUri(uint256 _projectId, string calldata _uri)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, Operations.SetUri)
```

```javascript
/**
  @notice 
  Allows a project owner to transfer its handle to another address.

  @dev 
  Only a project's owner or operator can transfer its handle.

  @param _projectId The ID of the project to transfer the handle from.
  @param _to The address that can now reallocate the handle.
  @param _newHandle The new unique handle for the project that will replace the transferred one.
*/
function transferHandle(
    uint256 _projectId,
    address _to,
    bytes32 _newHandle
)
    external
    override
    requirePermission(ownerOf(_projectId), _projectId, Operations.SetHandle)
    returns (bytes32 _handle)
```

```javascript
/**
  @notice 
  Allows an address to claim and handle that has been transferred to them and apply it to a project of theirs.

  @dev 
  Only a project's owner or operator can claim a handle onto it.

  @param _handle The handle being claimed.
  @param _for The address that the handle has been transferred to.
  @param _projectId The ID of the project to use the claimed handle.
*/
function claimHandle(
    bytes32 _handle,
    address _for,
    uint256 _projectId
)
    external
    override
    requirePermissionAllowingWildcardDomain(
        _for,
        _projectId,
        Operations.ClaimHandle
    )
    requirePermission(
        ownerOf(_projectId),
        _projectId,
        Operations.ClaimHandle
    )
```

```javascript
/** 
  @notice
  Allows anyone to challenge a project's handle. After one year, the handle can be claimed by the public if the challenge isn't answered by the handle's project.
  This can be used to make sure a handle belonging to an unattended to project isn't lost forever.

  @param _handle The handle to challenge.
*/
function challengeHandle(bytes32 _handle) external
```

```javascript
/** 
  @notice
  Allows a project to renew its handle so it can't be claimed until a year after its challenged again.

  @dev 
  Only a project's owner or operator can renew its handle.

  @param _projectId The ID of the project that current has the handle being renewed.
*/
function renewHandle(uint256 _projectId)
    external
    requirePermission(
        ownerOf(_projectId),
        _projectId,
        Operations.RenewHandle
    )
```
