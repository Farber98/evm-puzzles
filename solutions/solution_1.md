## EVM Mnemonic

```
00 34 CALLVALUE
01 56 JUMP
02 FD REVERT
03 FD REVERT
04 FD REVERT
05 FD REVERT
06 FD REVERT
07 FD REVERT
08 5B JUMPDEST
09 00 STOP
```

## Explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. Next, the JUMP instruction will read from
top of the stack to get the destination of the jump. JUMP must land at JUMPDEST instruction to solve the puzzle
and stop gracefully. This is achieved by sending the value 8, so JUMP will arrive to JUMPDEST (line 08).

## Detailed explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. We are sending value 8.

Stack before CALLVALUE

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after CALLVALUE

```
[8 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMP will read the value from the top of the stack and jump to that instruction. As the value at the top is 8,
the target instruction will be instruction 8, in other words, JUMPDEST.

So if we send the value 8, the program will do the following:

```
00 34 CALLVALUE
01 56 JUMP
08 5B JUMPDEST
09 00 STOP
```
