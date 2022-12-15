## EVM Mnemonic

```
00 34 CALLVALUE
01 38 CODESIZE
02 18 XOR
03 56 JUMP
04 FD REVERT
05 FD REVERT
06 FD REVERT
07 FD REVERT
08 FD REVERT
09 FD REVERT
0A 5B JUMPDEST
0B 00 STOP
```

## Explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. Next, the CODESIZE instruction will put the
nummber of instructions (or code size) on top of the stack, moving CALLVALUE to the second place. XOR will make and exclusive or comparison between second element of the stack and first one (STACK[0] XOR STACK[1]) and put the result on top of the stack. Next, the JUMP instruction will read from top of the stack to get the destination of the jump. JUMP must land at JUMPDEST instruction to solve the puzzle and stop gracefully. This is achieved by sending the value 6 (0110b), as CODESIZE is a fixed at 12 (1100b) so JUMP will arrive to JUMPDEST (line 10 (0x0A)).

## Detailed explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. We are sending value 6.

Stack before CALLVALUE

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after CALLVALUE

```
[6 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

CODESIZE instruction will put the number of instructions (12) on top of the stack, moving CALLVALUE to the second place.

Stack after CODESIZE

```
[12 6 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

XOR will make and exclusive or comparison between first and second elements of the stack (STACK[0] XOR STACK[1]) and put the result on top of the stack.

But wait, how does XOR operator works? In a nutshell, it returns 1 only if exactly one bit is set to 1 out of the two bits in comparison. Let's do it in binary, where 12 is equal to 1100b and 6 is equal to 0110b

```
1 1 0 0
  XOR
0 1 1 0
- - - -
1 0 1 0
```

So we get the value 1010b, as known as, 10 in decimal base.

Stack after XOR

```
[10 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMP will read the value from the top of the stack and jump to that instruction. As the value at the top is 10,
the target instruction will be instruction 10, in other words, JUMPDEST.

So if we send the value 6, the program will do the following:

```
00 34 CALLVALUE
01 38 CODESIZE
02 18 XOR
03 56 JUMP
0A 5B JUMPDEST
0B 00 STOP
```
