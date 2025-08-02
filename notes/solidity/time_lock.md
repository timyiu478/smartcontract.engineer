---
title: "Time Lock"
tags: ["Solidity", "App"]
---

# What is vault and How to calculate the shares



https://www.youtube.com/watch?v=k7WNibJOBXE

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

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract TimeLock {
    error NotOwnerError();
    error AlreadyQueuedError(bytes32 txId);
    error TimestampNotInRangeError(uint256 blockTimestamp, uint256 timestamp);
    error NotQueuedError(bytes32 txId);
    error TimestampNotPassedError(uint256 blockTimestmap, uint256 timestamp);
    error TimestampExpiredError(uint256 blockTimestamp, uint256 expiresAt);
    error TxFailedError();

    event Queue(
        bytes32 indexed txId,
        address indexed target,
        uint256 value,
        string func,
        bytes data,
        uint256 timestamp
    );
    event Execute(
        bytes32 indexed txId,
        address indexed target,
        uint256 value,
        string func,
        bytes data,
        uint256 timestamp
    );
    event Cancel(bytes32 indexed txId);

    uint256 public constant MIN_DELAY = 10; // seconds
    uint256 public constant MAX_DELAY = 1000; // seconds
    uint256 public constant GRACE_PERIOD = 1000; // seconds

    address public owner;
    // tx id => queued
    mapping(bytes32 => bool) public queued;

    modifier onlyOwner() {
        if (msg.sender != owner) {
            revert NotOwnerError();
        }
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    receive() external payable {}

    function getTxId(
        address target,
        uint256 value,
        string calldata func,
        bytes calldata data,
        uint256 timestamp
    ) public pure returns (bytes32) {
        return keccak256(abi.encode(target, value, func, data, timestamp));
    }

    /**
     * @param target Address of contract or account to call
     * @param value Amount of ETH to send
     * @param func Function signature, for example "foo(address,uint256)"
     * @param data ABI encoded data send.
     * @param timestamp Timestamp after which the transaction can be executed.
     */
    function queue(
        address target,
        uint256 value,
        string calldata func,
        bytes calldata data,
        uint256 timestamp
    ) external onlyOwner returns (bytes32 txId) {
        txId = getTxId(target, value, func, data, timestamp);
        if (queued[txId] != false) {
            revert AlreadyQueuedError(txId);
        }
        if (timestamp < (MIN_DELAY + block.timestamp) || timestamp > (MAX_DELAY + block.timestamp)) {
            revert TimestampNotInRangeError(block.timestamp, timestamp);
        }
        queued[txId] = true;
        emit Queue(txId, target, value, func, data, timestamp);
    }

    function execute(
        address target,
        uint256 value,
        string calldata func,
        bytes calldata data,
        uint256 timestamp
    ) external payable onlyOwner returns (bytes memory) {
        bytes32 txId = getTxId(target, value, func, data, timestamp);
        if (queued[txId] == false) {
            revert NotQueuedError(txId);
        }
        if (block.timestamp < timestamp || block.timestamp > (timestamp + GRACE_PERIOD)) {
            revert TimestampNotPassedError(block.timestamp, timestamp);
        }
        (bool success, bytes memory retdata) =
            target.call{value: value}(abi.encodeWithSignature(func, data));
        require(success == true, "call failed");
        queued[txId] = false;
        emit Execute(txId, target, value, func, data, timestamp);
        return retdata;
    }

    function cancel(bytes32 txId) external onlyOwner {
        if (queued[txId] == false) {
            revert NotQueuedError(txId);
        }
        queued[txId] = false;
        emit Cancel(txId);
    }
}
```
