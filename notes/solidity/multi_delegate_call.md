---
title: "Multi Delegate Call"
tags: ["Solidity", "App", "Contract"]
---

# Description

Let's say you need to call two functions in a smart contract. You will need to send two transactions.

MultiDelegatecall is a handy smart contract that enables a contract to execute multiple functions in a single transaction.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract MultiDelegatecall {
    function multiDelegatecall(bytes[] calldata data)
        external
        payable
        returns (bytes[] memory results)
    {
        results = new bytes[](data.length);
        for (uint256 i=0; i < data.length; i++) {
            (bool success, bytes memory retdata) =
            address(this).delegatecall(data[i]);
            require(success, "delegate call failed");
            results[i] = retdata;
        }
    }
}

contract TestMultiDelegatecall is MultiDelegatecall {
    event Log(address caller, string func, uint256 i);

    function func1(uint256 x, uint256 y) external {
        emit Log(msg.sender, "func1", x + y);
    }

    function func2() external returns (uint256) {
        emit Log(msg.sender, "func2", 2);
        return 111;
    }
}
```
