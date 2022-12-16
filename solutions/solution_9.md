## EVM Mnemonic

```
CALLDATASIZE
PUSH1 0x03
LT
PUSH1 0x09
JUMPI
REVERT
REVERT
JUMPDEST
CALLVALUE
CALLDATASIZE
MUL
PUSH1 0x08
EQ
PUSH1 0x14
JUMPI
REVERT
JUMPDEST
STOP
```

## Explanation

Here we have to pass a CALLDATAVALUE with size > 3B, so LT is met and first JUMP is achieved.

Next, we have to found a combination of values that makes CALLDATASIZE \* CALLVALUE = 8 while CALLDATASIZE > 3

The easiest thing is to use CALLDATA = 0x00000000 (4B size) and CALLVALUE = 2.

## Detailed explanation
