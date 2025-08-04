---
title: "Simple Bytecode Contract"
tags: ["Solidity", "Contract", "EVM"]
---

# Description

Write a smart contract that always return the number 255. The size of the smart contract must be less than or equal to 10 bytes.

# Tasks

## 1

Write the runtime code that will always return 255.

Store it as a comment for now.

## 2

Write the creation code for the runtime code from task 1.

Store it as a comment for now.

## 3

Complete the function deploy

```
function deploy() external {}
```

Deploy the bytecode from task 2
emit the event Log with the address of the deployed contract

# Solution

Useful Tool: https://www.evm.codes/playground

> From the EVM opcode point of view, memory is indeed treated as a flat, one-dimensional byte-addressable storage space

## 1. write the runtime code that return 255

EVM code:

```
PUSH1 0xFF
PUSH1 0 
MSTORE  // store 255 in memory[0]
PUSH1 0x20 // uint256 type uses 32 bytes
PUSH1 0 
RETURN // return memory[0:1]
```

Bytecode representation:

```
60ff60005260206000f3
```

## 2. write creation code

1. store runtime code in memory
2. return runtime code by specifying its memory region

EVM code:

```
PUSH10 0x60ff60005260206000f3
PUSH1 0 
MSTORE
PUSH1 0x0a // size is 10 bytes
PUSH1 0x16 // runtime code memory offset is 22
RETURN
```

Bytecode representation:

```
6960ff60005260206000f3600052600a6016f3
```

## 3.

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract Factory {
    event Log(address addr);

    function deploy() external returns (address) {
        bytes memory code = hex"6960ff60005260206000f3600052600a6016f3";
        address addr;
        assembly {
            // The length of a dynamic array is stored at the first slot of the array and followed by the array elements.
            // ref: https://docs.soliditylang.org/en/latest/internals/layout_in_memory.html
            let data := add(code, 0x20) // skip the length prefix
            addr := create(0, data, 0x16)
        }
        require(addr != address(0), "Failed to deploy");
        emit Log(addr);
        return addr;
    }
}
```
