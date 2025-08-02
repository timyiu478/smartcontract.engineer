---
title: "Vault"
tags: ["Solidity", "DeFi"]
---

# What is vault and How to calculate the shares

https://www.youtube.com/watch?v=k7WNibJOBXE

# Description

Vault is a contract where users deposit token to earn yield.

Deposit token to mint shares. Burn shares to withdraw token.

This challenge will focus on the math for minting and burning shares.

# Tasks

## 1

Declare state variables.

Total amount of shares.

```
uint256 public totalSupply
```

A mapping from user's address to amount of shares they have.


mapping(address => uint256) public balanceOf

## 2

Complete the function

```
function _mint(address to, uint256 shares) private {}
```

This is a private function to mint shares amount of shares to to address. Update the state variables balanceOf and totalSupply.

## 3

Complete the function

```
function _burn(address from, uint256 shares) private {
```

This is a private function to burn shares amount of shares from from address. Update the state variables balanceOf and totalSupply.

## 4

Complete the function

```
function deposit(uint256 amount) external {}
```

This function transfers amount amount of token from msg.sender, and mints the approriate amount of shares.

If total shares (totalSupply) is 0, then the amount of shares to mint equals the amount deposited.

Otherwise the amount of shares to mint should be proportional to the percent increase of total tokens held in this contract.

## 5

Complete the function

```
function withdraw(uint256 shares) external {}
```

This function burns shares amount of token from msg.sender, and transfers the appropriate amount of tokens.

The amount of tokens to receive must be proportional to the amount of shares gettings burnt.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

import {IERC20} from "sce/sol/IERC20.sol";

contract Vault {
    IERC20 public immutable token;

    constructor(address _token) {
        token = IERC20(_token);
    }

    uint256 public totalSupply;

    mapping(address => uint256) public balanceOf;

    function _mint(address to, uint256 shares) private {
        balanceOf[to] += shares;
        totalSupply += shares;
    }

    function _burn(address from, uint256 shares) private {
        balanceOf[from] -= shares;
        totalSupply -= shares;
    }

    function deposit(uint256 amount) external {
        uint256 shares = amount;
        if (totalSupply > 0) {
            shares = amount * totalSupply / token.balanceOf(address(this));
        }
        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), amount);
    }

    function withdraw(uint256 shares) external {
        require(balanceOf[msg.sender] >= shares, "Insuffient shares");
        
        uint256 amount = shares * token.balanceOf(address(this)) / totalSupply;
        
        _burn(msg.sender, shares);
        token.transfer(msg.sender, amount);
    }
}
```
