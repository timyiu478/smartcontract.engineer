---
title: "Delegate Call"
tags: ["Solidity", "Contract", "Function"]
---

# Description

delegatecall is like call, except the code of callee is executed inside the caller.

For example contract A calls delegatecall on contract B. Code inside B is executed using A's context such as storage, msg.sender and msg.value.

This is the contract that is called.

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract TestDelegateCall {
    // Storage layout must be the same as contract A
    uint256 public num;
    address public sender;
    uint256 public value;

    function setVars(uint256 _num) external payable {
        num = _num;
        sender = msg.sender;
        value = msg.value;
    }

    function setNum(uint256 _num) external {
        num = _num;
    }
}
```

# Task

Complete setNum. This function will call delegatecall on TestDelegateCall.setNum.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract DelegateCall {
    uint256 public num;
    address public sender;
    uint256 public value;

    function setVars(address test, uint256 _num) external payable {
        // This contract's storage is updated, TestDelegateCall's storage is not modified.
        (bool success, bytes memory data) =
            test.delegatecall(abi.encodeWithSignature("setVars(uint256)", _num));
        require(success, "tx failed");
    }

    function setNum(address test, uint256 _num) external {
      (bool success, bytes memory data) =
            test.delegatecall(abi.encodeWithSignature("setNum(uint256)", _num));
        require(success, "tx failed");
    }
}
```
