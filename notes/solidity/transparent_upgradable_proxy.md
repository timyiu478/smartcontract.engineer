---
title: "Transparent Upgradable Proxy"
tags: ["Solidity", "App"]
---

# Description

Implement a transparent upgradeable proxy.

This is the library contract to store address to any storage slot.

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

library StorageSlot {
    // Wrap address in a struct so that it can be passed around as a storage pointer
    struct AddressSlot {
        address value;
    }

    function getAddressSlot(
        bytes32 slot
    ) internal pure returns (AddressSlot storage pointer) {
        assembly {
            // Get the pointer to AddressSlot stored at slot
            pointer.slot := slot
        }
    }
}
```

These are the implementation contracts used for this challenge.

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract CounterV1 {
    uint256 public count;

    function inc() external {
        count += 1;
    }
}

contract CounterV2 {
    uint256 public count;

    function inc() external {
        count += 1;
    }

    function dec() external {
        count -= 1;
    }
}
```

# Solution


