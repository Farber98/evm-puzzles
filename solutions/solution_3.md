## EVM Mnemonic

```
00 36 CALLDATASIZE
01 56 JUMP
02 FD REVERT
03 FD REVERT
04 5B JUMPDEST
05 00 STOP
```

## Explanation

CALLDATASIZE gets the byte size sent from tx in calldata and puts it in front of the stack. Next, the JUMP instruction will read from top of the stack to get the destination of the jump. JUMP must land at JUMPDEST instruction to solve the puzzle and stop gracefully. This is achieved by sending any calldata with size of 4 bytes, so JUMP will arrive to JUMPDEST (line 4).

## Detailed explanation

CALLDATASIZE gets the byte size sent from tx in calldata and puts it in front of the stack. We are sending value 0x00000000, where every 2 hex characters we have a byte.

Stack before CALLDATASIZE

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after CALLDATASIZE

```
[4 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMP will read the value from the top of the stack and jump to that instruction. As the value at the top is 4,
the target instruction will be instruction 4, in other words, JUMPDEST.

So if we send the value 0x00000000, the program will do the following:

```
00 36 CALLDATASIZE
01 56 JUMP
04 5B JUMPDEST
05 00 STOP
```
