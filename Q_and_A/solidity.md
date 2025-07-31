# What is the difference between pure and view function?

- view functions can read state and global variables
- pure functions can not

# require vs revert vs assert

- require is used to validate inputs and check conditions before and after execution.
- revert is like require but more handy when the condition to check is nested in several if statements.
- assert is used to check invariants, code that should never be false. Failing assertion probably means that there is a bug.

# What is function modifier?

Function modifiers are reuseable code that can be run before and / or after a function call.

Here are some examples of how they are used.

- Restrict access
- Validate inputs
- Check states right before and after a function call

# Data location: memory vs calldata

Variables are stored in one of three places.

storage - variable is a state variable (store on blockchain)
memory - variable is in memory and it exists temporary during a function call
calldata - special data location that contains function arguments

calldata is like memory but **not modifiable**. calldata saves gas.

# Variable: constant vs immutable

Variables declared immutable are like constants, except their value can be set inside the constructor.

# Which function is called, fallback() or receive()?

```
    Ether is sent to contract
               |
        is msg.data empty? // empty means no function is specified
              / \
            yes  no
            /     \
receive() exists?  fallback()
         /   \
        yes   no
        /      \
    receive()   fallback()
```

# call vs delegate call

- call: executes code in the target contract’s context.
- delegate call: executes code from the target contract, but in the calling contract’s context.
