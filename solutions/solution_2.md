## EVM Mnemonic

```
00 34 CALLVALUE
01 38 CODESIZE
02 03 SUB
03 56 JUMP
04 FD REVERT
05 FD REVERT
06 5B JUMPDEST
07 00 STOP
08 FD REVERT
09 FD REVERT
```

## Explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. Next, the CODESIZE instruction will put the
nummber of instructions (or code size) on top of the stack, moving CALLVALUE to the second place. SUB will substract the second element of the stack to the first one (STACK[0] - STACK[1]) and put the result on top of the stack. Next, the JUMP instruction will read from top of the stack to get the destination of the jump. JUMP must land at JUMPDEST instruction to solve the puzzle and stop gracefully. This is achieved by sending the value 4, so JUMP will arrive to JUMPDEST (line 6).

## Detailed explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. We are sending value 4.

Stack before CALLVALUE

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after CALLVALUE

```
[4 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

CODESIZE instruction will put the number of instructions (10) on top of the stack, moving CALLVALUE to the second place.

Stack after CODESIZE

```
[10 4 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

SUB will substract the second element of the stack to the first one (STACK[0] - STACK[1] = 10 - 4 = 6) and put the result on top of the stack.

Stack after SUB

```
[6 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMP will read the value from the top of the stack and jump to that instruction. As the value at the top is 6,
the target instruction will be instruction 6, in other words, JUMPDEST.

So if we send the value 4, the program will do the following:

```
00 34 CALLVALUE
01 38 CODESIZE
02 03 SUB
03 56 JUMP
06 5B JUMPDEST
07 00 STOP
```
