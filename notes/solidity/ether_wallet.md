---
title: "Ether Wallet"
tags: ["Solidity", "Payment", "App"]
---

# Description

Create a contract that can receive Ether from anyone. Only the owner can withdraw.

# Tasks

## 1

Enable the contract to receive Ether.

## 2

Implement

```
function withdraw(uint256 amount) external
```

This function will send amount of Ether to owner. Only the owner can call this function.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract EtherWallet {
    address payable public owner;

    constructor() {
        owner = payable(msg.sender);
    }
    
    receive() external payable {}
    
    function withdraw(uint256 amount) external {
        require(msg.sender == owner);
        (bool sent, bytes memory data) = msg.sender.call{value: amount}("");
        require(sent, "Failed to send Ether");
    }
}
```
