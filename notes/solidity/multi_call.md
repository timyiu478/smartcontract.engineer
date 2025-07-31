---
title: "Access Control"
tags: ["Solidity", "App", "Contract"]
---

# Description

MultiCall is a handy contract that queries multiple contracts in a single function call and returns all the results.

This function can be modified to work with call or delegatecall.

However for this challenge you will use staticcall to query other contracts.

Here is the contract that will be called.

```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract TestMultiCall {
    function test(uint256 _i) external pure returns (uint256) {
        return _i;
    }
}
```

Staticcall
The syntax to query contracts using staticcall is similar to using call

```
function multiCall(address[] calldata targets, bytes[] calldata data)
```

# Tasks

## 1

This function should

- For each address in targets use staticcall to call targets[i] passing data[i].
- Fail if any call to address in targets fails
- Save the result in results bytes array.
- Return all the results stored in results.
- Fail if targets.length differs from data.length.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract MultiCall {
    function multiCall(address[] calldata targets, bytes[] calldata data)
        external
        view
        returns (bytes[] memory)
    {
        bytes[] memory results = new bytes[](data.length);
        
        require(targets.length == data.length, "mismatch between # of targets and # of data");
        
        for (uint256 i=0; i < targets.length; i++) {
            (bool success, bytes memory response) = targets[i].staticcall(data[i]);
            require(success == true, "call failed");
            results[i] = response;
        }
        

        return results;
    }
}
```
