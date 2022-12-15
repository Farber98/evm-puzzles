## EVM Mnemonic

```
00 34     CALLVALUE
01 80     DUP1
02 02     MUL
03 610100 PUSH2 0100
06 14     EQ
07 600C   PUSH1 0C
09 57     JUMPI
0A FD     REVERT
0B FD     REVERT
0C 5B     JUMPDEST
0D 00     STOP
0E FD     REVERT
0F FD     REVERT
```

## Explanation

- CALLVALUE gets value sent from tx and puts it in front of the stack.

- DUP1 instruction will duplicate the value from STACK[0] and put it on STACK[1].

- MUL will multiply the second element of the stack and first one (STACK[0] \* STACK[1]) and put the result on top of the stack.

- PUSH2 will push value 256 (0x0100 size 2 bytes) into top of the stack.

- EQ will compare first to elements of the stack, and put the result of STACK[0] == STACK[1] on top of the stack (1 if equality is met, 0 otherwise). PUSH2 will push value 12 (0x0C size 1 byte) into top of the stack.

- JUMPI instruction will ONLY JUMP to the instruction given by the value on the top of the stack if the second element of the stack is different than 0 (STACK[1] != 0 ? JUMP STACK[0] : execute next line).

- JUMPI must land at JUMPDEST instruction to solve the puzzle and stop gracefully. This is achieved by sending the value 16, so the EQ condition is met and STACK[1] != 0 when JUMPI evaluates and makes the jump to the value pushed by PUSH1 (line 12 (0x0C)) where JUMPDEST is located.

## Detailed explanation

CALLVALUE gets value sent from tx and puts it in front of the stack. We are sending value 16.

Stack before CALLVALUE

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after CALLVALUE

```
[16 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

DUP1 instruction will duplicate the value from STACK[0] and put it on STACK[1].

Stack after DUP1

```
[16 16 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

MUL will multiply the second element of the stack with the first one (STACK[0] \* STACK[1]) and put the result on top of the stack.

Stack after MUL

```
[256 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

PUSH2 will push value 256 (0x0100 size 2 bytes) on top of the stack.

Stack after PUSH2

```
[256 256 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

EQ will compare first to elements of the stack, and put the result of STACK[0] == STACK[1] on top of the stack (1 if equality is met, 0 otherwise).

Stack after PUSH2

```
[1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

PUSH1 will push value 12 (0x0C size 1 byte) on top of the stack.

Stack after PUSH1

```
[12 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMPI instruction will ONLY JUMP to the instruction given by the value on the top of the stack if the second element of the stack is different than 0 (STACK[1] != 0 ? JUMP STACK[0] : execute next line).

JUMPI will read the value at STACK[1] = 1 != 0, so it will JUMP to STACK[0] = 0x0C instruction, in other words, JUMPDEST.

So if we send the value 16, the program will do the following:

```
00 34     CALLVALUE
01 80     DUP1
02 02     MUL
03 610100 PUSH2 0100
06 14     EQ
07 600C   PUSH1 0C
09 57     JUMPI
0C 5B     JUMPDEST
0D 00     STOP
```
