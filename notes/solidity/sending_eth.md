---
title: "Sending Ether"
tags: ["Solidity", "Payment"]
---

# Description

Ether can be sent from a contract to another address in 3 ways, transfer, send and call.

How are transfer, send and call different?

- transfer (forwards 2300 gas, throws error on failure)
- send (forwards 2300 gas, returns bool)
- call (forwards specified gas or defaults to all, returns bool and outputs in bytes)

Which function should you use?

call is the recommended method to use for security reasons.

---

# Tasks

## 1

Enable this contract to receive Ether.

## 2

Add

```
function sendEth(address payable to, uint256 amount) external
```

This function will send amount Ether to address to.

---

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract SendEther {
    function sendViaTransfer(address payable to) external payable {
        // This function is no longer recommended for sending Ether.
        to.transfer(msg.value);
    }

    function sendViaSend(address payable to) external payable {
        // Send returns a boolean value indicating success or failure.
        // This function is not recommended for sending Ether.
        bool sent = to.send(msg.value);
        require(sent, "Failed to send Ether");
    }

    function sendViaCall(address payable to) external payable {
        // Call returns a boolean value indicating success or failure.
        // This is the current recommended method to use.
        (bool sent, bytes memory data) = to.call{value: msg.value}("");
        require(sent, "Failed to send Ether");
    }
    
    receive() external payable {}
    
    function sendEth(address payable to, uint256 amount) external payable {
        (bool sent, bytes memory data) = to.call{value: amount}("");
        require(sent, "Failed to send Ether");
    }
}
```
