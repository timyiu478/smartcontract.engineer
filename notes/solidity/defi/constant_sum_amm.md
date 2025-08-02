---
title: "Constant Sum Automated Market Maker"
tags: ["Solidity", "DeFi"]
---

# Constant Sum AMM Math

- amount of token in = amount of token out
- increase in liquidity is proportional to increase in total shares

https://www.youtube.com/watch?v=-JhgcqvyYeM

# Description

Constant sum AMM (automated market maker) is a decentralized exchange that trades two tokens in the contract.

Users will be able to add liquidity and earn swap fees, swap tokens and remove liquidity.

## Why is it called constant sum?

The amount of token to come out of the trade is determined by the following equation.

```
X = amount of token A in the contract
Y = amount of token B in the contract
K = a constant that satisfies the following equation

X + Y = K
X + Y equals some constant K, hence the name constant sum.
```

This equation X + Y = K must be true before and after every swap.

```
dx = Amount of token A that came in from the swap
dy = Amount of token B that went out from the swap
```

Before the trade:

```
X + Y = K
```

After the trade:

```
X + dx + Y - dy = K
```

Since X + Y = K, replace K in the second equation X + dx + Y - dy = K with X + Y

```
X + dx + Y - dy = X + Y
```

Cancel out X and Y on both side of the equation

```
dx - dy = 0
or simply


dx = dy
```

In other words, the amount of token coming in from a swap must equal the amount of token going out.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

import {IERC20} from "sce/sol/IERC20.sol";

contract CSAMM {
    IERC20 public immutable token0;
    IERC20 public immutable token1;

    uint256 public reserve0;
    uint256 public reserve1;

    uint256 public totalSupply;
    mapping(address => uint256) public balanceOf;

    constructor(address _token0, address _token1) {
        // NOTE: This contract assumes that token0 and token1
        // both have same decimals
        token0 = IERC20(_token0);
        token1 = IERC20(_token1);
    }

    function _mint(address to, uint256 amount) private {
        totalSupply += amount;
        balanceOf[to] += amount;
    }

    function _burn(address from, uint256 amount) private {
        totalSupply -= amount;
        balanceOf[from] -= amount;
    }

    function swap(address _tokenIn, uint256 _amountIn)
        external
        returns (uint256 amountOut)
    {
        require(IERC20(_tokenIn) == token0 || IERC20(_tokenIn) == token1, "Invalid tokens");
        
        if (IERC20(_tokenIn) == token0) {
            token0.transferFrom(msg.sender, address(this), _amountIn);
            token1.transfer(msg.sender, _amountIn * 997 / 1000);
            reserve0 += _amountIn;
            reserve1 -= _amountIn * 997 / 1000;
        } else {
            token1.transferFrom(msg.sender, address(this), _amountIn);
            token0.transfer(msg.sender, _amountIn * 997 / 1000);
            reserve1 += _amountIn;
            reserve0 -= _amountIn * 997 / 1000;
        }
    }

    function addLiquidity(uint256 amount0, uint256 amount1)
        external
        returns (uint256 shares)
    {
        require(amount0 + amount1 > 0, "Cant add 0 liquidity");
        
        if ((token0.balanceOf(address(this)) + token1.balanceOf(address(this))) > 0) {
            shares = (amount0 + amount1) * totalSupply / 
                (token0.balanceOf(address(this)) + token1.balanceOf(address(this)));
        } else {
            shares = (token0.balanceOf(address(this)) + token1.balanceOf(address(this)));
        }
        
        token0.transferFrom(msg.sender, address(this), amount0);
        token1.transferFrom(msg.sender, address(this), amount1);
        
        _mint(msg.sender, amount0 + amount1);
        
        reserve0 += amount0;
        reserve1 += amount1;
        
    }

    function removeLiquidity(uint256 shares)
        external
        returns (uint256 d0, uint256 d1)
    {
        
        uint256 amount = 
            shares * (token0.balanceOf(address(this)) + token1.balanceOf(address(this))) /
            totalSupply;
        
        d0 = shares * token0.balanceOf(address(this)) / totalSupply;
        d1= shares * token1.balanceOf(address(this)) / totalSupply;
        
        _burn(msg.sender, amount);
        
        token0.transfer(msg.sender, d0);
        token1.transfer(msg.sender, d1);
        
        reserve0 -= d0;
        reserve1 -= d1;
    }
}
```
