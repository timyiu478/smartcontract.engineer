---
title: "Time Lock"
tags: ["Solidity", "App"]
---

# Description

TimeLock is a contract where transactions must be queued for some minimum time before it can be executed.

It's usually used in DAO to increase transparency. Call to critical functions are restricted to time lock.

This give users time to take action before the transaction is executed by the time lock.

For example, TimeLock can be used to increase users' trust that the DAO will not rug pull.

Here is the contract that will be used to test TimeLock

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract TestTimeLock {
    address public timeLock;
    bool public canExecute;
    bool public executed;

    constructor(address _timeLock) {
        timeLock = _timeLock;
    }

    fallback() external {}

    function func() external payable {
        require(msg.sender == timeLock, "not time lock");
        require(canExecute, "cannot execute this function");
        executed = true;
    }

    function setCanExecute(bool _canExecute) external {
        canExecute = _canExecute;
    }
}
```

# Tasks

## 1

Complete the function

```
function queue(
    address target,
    uint256 value,
    string calldata func,
    bytes calldata data,
    uint256 timestamp
) external returns (bytes32 txId) {
    // code
}
```

This function will queue a transaction to be executed later.

- Only owner can call
- Revert if transaction is already queued
- Revert if timestamp is less than MIN_DELAY or greater than MAX_DELAY from block.timestamp
- Compute the transaction hash by calling getTxId and queue the transaction into the mapping queued
- emit the event Queue
- Return the transaction id that was queued

## 2

Complete the function

```
function execute(
    address target,
    uint256 value,
    string calldata func,
    bytes calldata data,
    uint256 timestamp
) external payable returns (bytes memory) {
    // code
}
```

This function will execute a transaction that's been queued.

- Only owner can call.
- Revert if transaction is not queued
- Revert if current timestamp is less tnan timestamp or if it is beyond timestamp + GRACE_PERIOD
- Remove transaction from queue
- Prepare the data to send to target. If func is not empty, then compute the function selector of func and prepend it to data.
- Call target sending value, revert if the call fails
- emit the event Execute
- Return data returned from calling target

## 3

Complete the function

```
function cancel(bytes32 txId) external {
    // code
}
```

This function will cancel a queued transaction.

Only owner can call.
Revert if transaction is not queued
Remove transaction from queue
emit the event Cancel

# Solution

todo
