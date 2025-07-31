
# Tasks

## 1

When this contract is created, initiallize owner to msg.sender.

## 2

Create a function modifier onlyOwner that will restrict function calls to the current owner.

It should checks that the caller is the current owner before executing the rest of the code, otherwise throw an error with the message "not owner".

## 3


Create an external function setOwner(address newOwner) which will set ownertonewOwner.

This function can only be called by the current owner.

Check that newOwner is not address(0) and throw an error "new owner = zero address" if it is.

---

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract Ownable {
    address public owner;
    
    constructor () {
        owner = msg.sender;
    }
    
    modifier onlyOwner {
        require(owner == msg.sender, "not owner");
        _;
    }
    
    function setOwner(address newOwner) external onlyOwner {
        owner = newOwner;
    }
}
```
