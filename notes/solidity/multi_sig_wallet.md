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

```
