---
title: "Wrapped ETH"
tags: ["Solidity", "DeFi"]
---

# Description

Wrapped Ether (WETH) is an ERC20 token that is fully backed by ETH.

Deposit ETH to mint WETH, burn WETH to withdraw ETH.

> WETH token is 1 to 1 map to ETH.

# Tasks

## 1

Declare ERC20 as the parent contract of WETH.

Next, initialize the constructor of ERC20 with the following inputs.

Name: Wrapped Ether
Symbol: WETH
Decimals: 18

Here is the constructor of ERC20:

```
constructor(string memory _name, string memory _symbol, uint8 _decimals) {
    name = _name;
    symbol = _symbol;
    decimals = _decimals;

    // ... more code ....
}
```

## 2

Complete the function

```
function deposit() public payable {}
```

This function will mint WETH to msg.sender for the amount of ETH that was sent.

Use the internal function _mint inherited from ERC20 to mint WETH.

```
function _mint(address to, uint256 amount) internal virtual {
    totalSupply += amount;

    unchecked {
        balanceOf[to] += amount;
    }

    emit Transfer(address(0), to, amount);
}
```

Lastly, emit the event Deposit.

## 3

Directly sending ETH to this contract should trigger the deposit function.

## 4

Complete the function

```
function withdraw(uint256 _amount) external {}
```

This function will burn amount of WETH from msg.sender and send the same amount of ETH to the caller.

Use the internal function _burn inherited from ERC20 to burn WETH.

```
function _burn(address from, uint256 amount) internal virtual {
    balanceOf[from] -= amount;

    unchecked {
        totalSupply -= amount;
    }

    emit Transfer(from, address(0), amount);
}
```

Lastly, emit the event Withdraw.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

import {ERC20} from "sce/sol/ERC20.sol";

contract WETH is ERC20 {
    event Deposit(address indexed account, uint256 amount);
    event Withdraw(address indexed account, uint256 amount);

    constructor() ERC20("Wrapped Ether", "WETH", 18) {}

    receive() external payable {
        deposit();
    }

    function deposit() public payable {
        _mint(msg.sender, msg.value);
        emit Deposit(msg.sender, msg.value);
    }

    function withdraw(uint256 amount) external {
        _burn(msg.sender, amount);
        msg.sender.call{value: amount}("");
        emit Withdraw(msg.sender, amount);
    }
}
```
