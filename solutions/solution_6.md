## EVM Mnemonic

```
00 6000 PUSH1 00
02 35   CALLDATALOAD
03 56   JUMP
04 FD   REVERT
05 FD   REVERT
06 FD   REVERT
07 FD   REVERT
08 FD   REVERT
09 FD   REVERT
0A 5B   JUMPDEST
0B 00   STOP
```

## Explanation

- PUSH1 will push value 0 on top of the stack.

- CALLDATALOAD instruction will get the 32-byte calldata value, starting from given offset by top of the stack, and puts the new value on top of the stack.

- JUMP instruction will read from top of the stack to get the destination of the jump. JUMP must land at JUMPDEST instruction to solve the puzzle and stop gracefully. This is achieved by sending the value 0x000000000000000000000000000000000000000000000000000000000000000A as calldata so JUMP will arrive to JUMPDEST (line 10 (0x0A)).

## Detailed explanation

PUSH1 will push value 0 on top of the stack.

Stack before PUSH1

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after PUSH1

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

CALLDATALOAD instruction will get the 32-byte calldata value, starting from given offset by top of the stack, and puts the new value on top of the stack. Because offset (top of the stack) is 0, we need to send the value of the JUMPDEST instruction as the less significant hex characters. JUMPDEST is located at 0x0A, so we need to send 0x000000000000000000000000000000000000000000000000000000000000000A.

Stack after CALLDATALOAD

```
[A 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMP will read the value from the top of the stack and jump to that instruction. As the value at the top is 0x0A,
the target instruction will be instruction 0x0A, in other words, JUMPDEST.

So if we send the CALLDATA value 0x000000000000000000000000000000000000000000000000000000000000000A, the program will do the following:

```
00 6000 PUSH1 00
02 35   CALLDATALOAD
03 56   JUMP
0A 5B   JUMPDEST
0B 00   STOP
```
