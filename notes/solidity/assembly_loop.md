---
title: "Assembly Loop"
tags: ["Solidity", "Assembly"]
---

# Tasks

## 1

Complete the function

```
function sum(uint256 n) public pure returns (uint256 z) {
  // Code here
}
```

Use assembly to calculate the sum of all integers from 1 to n - 1.

## 2

Complete the function

```
function pow2k(uint256 x, uint256 n) public pure returns (uint256 z) {
    require(x > 0, "x = 0");
    // Code here
}
```

This function will calculate x ** n where n = 2**k.

You can assume that x > 0 and that x**n is small enough that no overflow will occur.
Revert if n is not a power of 2, n != 2**k for some integer k.

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract AssemblyLoop {
    function yul_for_loop() public pure returns (uint256 z) {
        assembly {
            for { let i := 0 } lt(i, 10) { i := add(i, 1) } { z := add(z, 1) }
        }
    }

    function yul_while_loop() public pure returns (uint256 z) {
        assembly {
            let i := 0
            for {} lt(i, 5) {} {
                i := add(i, 1)
                z := add(z, 1)
            }
        }
    }

    function sum(uint256 n) public pure returns (uint256 z) {
         assembly {
            let i := 1
            for {} lt(i, n) {} {
                z := add(z, i)
                i := add(i, 1)
            }
        }
    }

    // Calculate x**n where n = 2**k
    // x > 0
    // No overflow check
    function pow2k(uint256 x, uint256 n) public pure returns (uint256 z) {
        require(x > 0, "x = 0");
        assembly {
            let m := 2
            for {} lt(m, n) {} {
                m := mul(m, 2)
            }
            
            // check if n is not 0 and n is not z**k
            if and(iszero(eq(n, m)), iszero(iszero(n))) {
                revert(0, 0)
            }
            
            z := exp(x, n)
            
        }
    }
}
```
