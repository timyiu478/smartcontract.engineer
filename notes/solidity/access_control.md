---
title: "Access Control"
tags: ["Solidity", "App"]
---

# Tasks

1. Define a new role named USER, use the keccak256 hash of the string "USER" as an identifier for this role.
1. Define modifier named onlyRole(bytes32 role) that checks msg.sender has role before executing the rest of the code.
1. Complete function _grantRole. This function will set role for account to true and then emit the event GrantRole.
1. Complete the external function grantRole. This function must restrict access only to msg.sender having the ADMIN role.
1. Complete the external function revokeRole that will revoke role from account. This function must restrict access only to msg.sender having the ADMIN role. Emit the event RevokeRole
1. Grant role ADMIN to msg.sender when this contract is deployed.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract AccessControl {
    event GrantRole(bytes32 indexed role, address indexed account);
    event RevokeRole(bytes32 indexed role, address indexed account);

    mapping(bytes32 => mapping(address => bool)) public roles;

    bytes32 public constant ADMIN = keccak256(abi.encodePacked("ADMIN"));
    bytes32 public constant USER = keccak256(abi.encodePacked("USER"));
    
    modifier onlyRole(bytes32 role) {
        require(roles[role][msg.sender] == true, "invalid user");
        _;
    }

    constructor() {
        roles[ADMIN][msg.sender] = true;
    }

    function _grantRole(bytes32 role, address account) internal {
        roles[role][account] = true;
    }

    function grantRole(bytes32 role, address account) external onlyRole(ADMIN) {
        _grantRole(role, account);
        emit GrantRole(role, account);
    }

    function revokeRole(bytes32 role, address account) external onlyRole(ADMIN) {
        roles[role][account] = false;
        emit RevokeRole(role, account);
    }
}
```
