---
title: "Multi-Sig Wallet"
tags: ["Solidity", "Payment", "App"]
---

# Description

Implement a multi sig wallet.

---

# Tasks

## 1

Enable this contract to receive Ether. Emit Deposit.

## 2

Write a function to submit a transaction to be approved by other owners.

- Function submit(address to, uint256 value, bytes calldata data) external stores the inputs into the array transactions
- Emits Submit with txId equal to the index where the transaction is stored in the array transactions
- Only the owners can call this function

## 3

Write function to approve a pending transaction.

- approve(uint256 txId) approves the pending transaction with the id txId.
- this function is external
- only owners can call this function
- transaction having txId must exist
- transaction must not be executed yet
- transaction must not be approved by msg.sender
- emit Approve

## 4

Write function to execute a pending transaction where number of approvals is greater than or equal to required.

- execute(uint256 txId) executes transaction at transcations[txId]
- this function is external
- only owners can call
- transcaction must exist
- transaction must not be executed yet
- number of approvals for this transaction must be greater than or equal to required
- emit Execute

## 5

Write function to cancel approval.

- revoke(uint256 txId) cancels approval of transaction txId approved by msg.sender
- this function is external
- only owners can call
- transaction must exist
- transaction must not be executed yet
- check that transaction is approved by msg.sender
- emit Revoke

---

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract MultiSigWallet {
    event Deposit(address indexed sender, uint256 amount);
    event Submit(uint256 indexed txId);
    event Approve(address indexed owner, uint256 indexed txId);
    event Revoke(address indexed owner, uint256 indexed txId);
    event Execute(uint256 indexed txId);

    struct Transaction {
        address to;
        uint256 value;
        bytes data;
        bool executed;
    }

    address[] public owners;
    mapping(address => bool) public isOwner;
    uint256 public required;

    Transaction[] public transactions;
    // mapping from tx id => owner => bool
    mapping(uint256 => mapping(address => bool)) public approved;

    modifier onlyOwner() {
        require(isOwner[msg.sender], "not owner");
        _;
    }

    modifier txExists(uint256 txId) {
        require(txId < transactions.length, "tx does not exist");
        _;
    }

    modifier notApproved(uint256 txId) {
        require(!approved[txId][msg.sender], "tx already approved");
        _;
    }

    modifier notExecuted(uint256 txId) {
        require(!transactions[txId].executed, "tx already executed");
        _;
    }

    constructor(address[] memory _owners, uint256 _required) {
        require(_owners.length > 0, "owners required");
        require(
            _required > 0 && _required <= _owners.length,
            "invalid required number of owners"
        );

        for (uint256 i; i < _owners.length; i++) {
            address owner = _owners[i];

            require(owner != address(0), "invalid owner");
            require(!isOwner[owner], "owner is not unique");

            isOwner[owner] = true;
            owners.push(owner);
        }

        required = _required;
    }
    
    receive() external payable {
        emit Deposit(msg.sender, msg.value);
    }
    
    function submit(address to, uint256 value, bytes calldata data) external onlyOwner {
        uint256 txId = transactions.length;
        Transaction memory t = Transaction(to, value, data,false); 
        transactions.push(t);
        approved[txId][msg.sender] = false;
        emit Submit(txId);
    }
    
    function approve(uint256 txId) external onlyOwner txExists(txId) notExecuted(txId) notApproved(txId) {
        approved[txId][msg.sender] = true;
        emit Approve(msg.sender, txId);
    }
    
    function execute(uint256 txId) external onlyOwner txExists(txId) notExecuted(txId) {
        uint256 approvalCount = 0;
        for (uint256 i; i < owners.length; i++) {
            approvalCount += approved[txId][owners[i]] == true ? 1 : 0;
        }
        require(approvalCount >= required, "Insuffient # of approvals");
        (bool sent, bytes memory data) = 
        transactions[txId].to.call{value: transactions[txId].value}(transactions[txId].data);
        require(sent, "Failed to send Ether");

        transactions[txId].executed = true;
        emit Execute(txId);        
    }
    
    function revoke(uint256 txId) external onlyOwner txExists(txId) notExecuted(txId) {
        require(approved[txId][msg.sender] == true, "Not approved");
        approved[txId][msg.sender] = false;
        emit Revoke(msg.sender, txId);
    }
}
```
