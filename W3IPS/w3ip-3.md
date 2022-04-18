---
w3ip: 3
title: Directory Standard
author: Qi Zhou (@qizhou)
type: Standards Track
category: W3IP
status: Draft
created: 2022-02-21
---

## Simple Summary

A standard interface for filesystem directories.

## Abstract

The following standard allows for the implementation of a standard API for filesystem directories within smart contracts.
This standard provides basic functionality to read/write binary objects for any size, as well as allow reading/writing chunks of the object if the object is too large to fit in a single transaction.

## Motivation

A standard interface allows any binary objects on EVM-based blockchain to be re-used by other dApps.

## Specification

## Directory

### Methods

#### write

Writes binary `data` to the file `name` in the directory by an account with write permission.

```
function write(bytes memory name, bytes memory data) external payable
```

#### read

Returns the binary `data` from the file `name` in the directory and existence of the file.

```
function read(bytes memory name) external view returns (bytes memory data, bool exist)
```

#### fallback read

Returns the binary `data` from the file `cdata` (prefixed with `/`) in the directory.

```
fallback(bytes calldata cdata) external returns (bytes memory data) 
```

#### size

Returns the size of the `data` from the file `name` in the directory and the number of chunks of the data.

```
function size(bytes memory name) external view returns (uint256 size, uint256 chunks)
```

#### remove

Removes the file `name` in the directory and returns the number of chunks removed (0 means the file does not exist) by an account with write permission.

```
function remove(bytes memory name) external returns (uint256 numOfChunksRemoved)
```


#### refund

Refund the token in the contract to the owner.

```
function refund() external
```

#### destruct()

Destory the contract.

```
function destruct() external
```

#### countChunks

Returns the number of chunks of the file `name`.

```
function countChunks(bytes memory name) external view returns (uint256 numOfChunks);
```

#### writeChunk

Writes a chunk of data to the file by an account with write permission. The write will fail if `chunkId > numOfChunks`, i.e., the write must append the file or replace the existing chunk.

```
 function writeChunk(bytes memory name, uint256 chunkId, bytes memory chunkData) external payable;
```

#### readChunk

Returns the chunk data of the file `name` and the existence of the chunk.

```
function readChunk(bytes memory name, uint256 chunkId) external view returns (bytes memory chunkData, bool exist);
```

#### chunkSize

Returns the size of a chunk of the file `name` and the existence of the chunk.

```
function chunkSize(bytes memory name, uint256 chunkId) external view returns (uint256 chunkSize, bool exist);
```

#### removeChunk

Removes a chunk of the file `name` and returns `false` if such chunk does not exist. The method should be called by an account with write permission.

```
function removeChunk(bytes memory name, uint256 chunkId) external returns (bool exist);
```

#### truncate

Removes the chunks of the file `name` in the directory from the given `chunkId` and returns the number of chunks removed by an account with write permission.  When `chunkId = 0`, the method is essentially the same as `remove()`.

```
function truncate(bytes memory name, uint256 chunkId) external returns (uint256 numOfChunksRemoved);
```


## Rationle

With EIP-4804, we are able to locate a Web3 resource on blockchain using HTTP-style URIs.  One application of Web3 resources are web contents that are referenced within a directory using relative paths such as HTML/SVG.  This standard proposes a contract-based directory to simplify the synchronization between local web contents and on-chain web contents.  Further, with relative paths referenced in the web contents and EIP-4804, the users will have a consistent view of the web contents locally and on-chain.

One issue of uploading the web contents to the blockchain is that the web contents may be too large to fit into a single transaction.  As a result, the standard provides a chunk-based operations so that uploading a content can be splitted into several transactions.  Meanwhile, the read operation can be done in a single transaction, i.e., with a single Web3 URL defined in EIP-4804.

## Reference Implementation

An example of the implementation can be found [here](https://github.com/web3q/evm-large-storage/blob/main/contracts/examples/FlatDirectory.sol)

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
