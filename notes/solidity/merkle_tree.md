---
title: "Merkle Tree"
tags: ["Solidity", "Data Structure"]
---

# Description

Implement algorithms for sorted Merkle tree.

# Tasks

## 1

Implement a function to calculate the Merkle root.

```
function calcRoot(bytes32[] memory hashes)
    internal
    pure
    returns (bytes32) {}
```

## 2

Implement the function that returns a proof given the leaf hashes and index of the leaf.

```
function getProof(bytes32[] memory hashes, uint256 index)
    internal
    pure
    returns (bytes32[] memory) {}
```

## 3

Implement the function that verifies the validity of a Merkle proof.

```
function verify(bytes32[] memory proof, bytes32 root, bytes32 leaf)
    internal
    pure
    returns (bool) {}
```

# Solution

todo
