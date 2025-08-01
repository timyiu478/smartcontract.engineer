---
title: "Gasless Token Transfer"
tags: ["Solidity", "App"]
---

# Description

Implement a gas-less token transfer.

---

# Task

Complete the function

```
function send(
    address token,
    address sender,
    address receiver,
    // Amount to send to receiver
    uint256 amount,
    // Fee paid to msg.sender
    uint256 fee,
    // Deadline for permit
    uint256 deadline,
    // Permit signature
    uint8 v,
    bytes32 r,
    bytes32 s
) external {
    // Write your code here
}
```

Call IERC20Permit(token).permit to update approval from sender to this contract.
Here is the interface for permit.

```
function permit(
    address owner,
    address spender,
    uint256 value,
    uint256 deadline,
    uint8 v,
    bytes32 r,
    bytes32 s
) external;
```

- owner is sender.
- spender is address(this).
- value is amount + fee.
- Transfer token from sender to receiver for the amount amount.
- Transfer token from sender to msg.sender for the amount fee.

---

# Solution


