---
title: "Token Lock"
tags: ["Solidity", "DeFi"]
---

# Description

TokenLock is a utility contract designed to lock tokens and release them gradually over time using a linear release schedule.

It can be particularly useful when combined with a vault to mitigate sandwich attacks during the claiming of profits.

# Tasks

## 1

```
function unlocked(address token) public view returns (uint256) {}
```

This function will calculate the amount of token that can be unlocked.

The amount to unlock increases linearly over time. At and after expiration (block.timestamp >= lock.expiresAt) the full amount is unlocked.

## 2

```
function claimable(address token) external view returns (uint256) {}
```

This function will return the **cumulative** amount that is unlocked.

When the function locked is called, it will store the amount unlocked into the mapping freed.

When the function unlock is called, freed is reset to 0.

Return the sum of the amounts stored in freed and the current unlocked amount.

## 3

```
function lock(address token, uint256 amount) external auth {}
```

This function locks amount of token and unlocks the appropriate amount that was previously locked.

- Free up the appropriate amount of token and allocate it to the mapping freed.
- Update the lock timestamps. updatedAt to block.timestamp and expiresAt to block.timestamp + lock duration.
- Transfer amount of token from msg.sender into this contract.
- Increment the lock for token by amount.

## 4

```
function unlock(address token) external auth returns (uint256 amount) {}
```

This function will unlock the appropriate amount of tokens and transfer the amount unlocked + the cumulative amount stored in freed.

- Calculate the amount to unlock
- Update the lock
- Reset freed[token] to 0
- Transfer the appropriate amount of token to msg.sender

## 5

```
function sync(address token) external auth {}
```

This function will transfer out excess amount of token from the contract.

Excess amount is whatever amount that is more than freed[token] + locked amount.

# Key Challenges to me

Linear unlock over time.

- Equation: unlocked(time) = amount * (t - updateAt) / (expiredAt - updateAt)

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

import {IERC20} from "sce/sol/IERC20.sol";

contract Auth {
    mapping(address => bool) public authorized;

    modifier auth() {
        require(authorized[msg.sender], "not authorized");
        _;
    }

    constructor() {
        authorized[msg.sender] = true;
    }

    function allow(address user) external auth {
        authorized[user] = true;
    }

    function deny(address user) external auth {
        authorized[user] = false;
    }
}

contract TokenLock is Auth {
    struct Lock {
        // Locked amount
        uint256 amount;
        // Last lock timestamp
        uint32 updatedAt;
        // Lock expiry timestamp
        uint32 expiresAt;
        // Lock duration
        uint32 duration;
    }

    mapping(address => Lock) public locks;
    mapping(address => uint256) public freed;

    function get(address token) external view returns (Lock memory) {
        return locks[token];
    }

    function set(address token, uint32 duration) external auth {
        locks[token].duration = duration;
    }

    function unlocked(address token) public view returns (uint256) {
        // block.timestamp >= expiresAt >= updatedAt
        // block.timestamp >= updateAt >= expiresAt
        // expiresAt > block.timestamp >= updateAt
        if (block.timestamp >= locks[token].expiresAt) {
            return locks[token].amount;
        }
        return locks[token].amount * (block.timestamp - uint256(locks[token].updatedAt)) / uint256(locks[token].expiresAt - locks[token].updatedAt);
    }

    function claimable(address token) external view returns (uint256) {
        return freed[token] + unlocked(token);
    }

    function lock(address token, uint256 amount) external auth {
        uint256 free = unlocked(token);
        
        freed[token] += free;
        
        locks[token].amount -= free;
        locks[token].updatedAt = uint32(block.timestamp);
        locks[token].expiresAt = uint32(block.timestamp) + locks[token].duration;
        
        if (amount > 0) {
            locks[token].amount += amount;
            IERC20(token).transferFrom(msg.sender, address(this), amount);
        }
    }

    function unlock(address token) external auth returns (uint256 amount) {
        uint256 free = unlocked(token);
        amount = freed[token] + free;
        
        freed[token] = 0;
        
        locks[token].amount -= free;
        locks[token].updatedAt = uint32(block.timestamp);
        
        if (amount > 0) {
            IERC20(token).transfer(msg.sender, amount);
        }
    }

    function sync(address token) external auth {
        IERC20(token).transfer(msg.sender, IERC20(token).balanceOf(address(this)) - locks[token].amount - freed[token]);
    }
}
```
