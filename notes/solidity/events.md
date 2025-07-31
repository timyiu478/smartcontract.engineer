---
title: "Event"
tags: ["Solidity", "Event"]
---

# Description

Events allow smart contracts to log data to the blockchain without using state variables.

Events are commonly used for debugging, monitoring and a cheap alternative to state variables for storing data.

---

# Tasks 

## 1

Declare an event named Message with 3 parameters.

- address named from with index
- address named to with index
- string named message

## 2

Create function function sendMessage(address addr, string calldata message) external {}
This function logs Message from msg.sender to the addr, with the message message.

---

## Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract Event {
    event Log(string message, uint256 val);
    // Up to 3 parameters can be indexed
    event IndexedLog(address indexed sender, uint256 val);

    event Message(address indexed from, address indexed to, string message);

    function examples() external {
        emit Log("Foo", 123);
        emit IndexedLog(msg.sender, 123);
    }
    
    function sendMessage(address addr, string calldata message) external {
        emit Message(msg.sender, addr, message);
    }
}
```
