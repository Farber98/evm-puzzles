## EVM Mnemonic

```
00 36   CALLDATASIZE
01 6000 PUSH1 00
03 80   DUP1
04 37   CALLDATACOPY
05 36   CALLDATASIZE
06 6000 PUSH1 00
08 6000 PUSH1 00
0A F0   CREATE
0B 3B   EXTCODESIZE
0C 6001 PUSH1 01
0E 14   EQ
0F 6013 PUSH1 13
11 57   JUMPI
12 FD   REVERT
13 5B   JUMPDEST
14 00   STOP
```

## Explanation

- CALLDATASIZE gets the byte size sent from tx in calldata and puts it in front of the stack.

- PUSH1 will push a value on top of the stack.

- DUP1 instruction will duplicate the value from STACK[0] and put it on STACK[1].

- CALLDATACOPY will grab the the third upper elements of the stack, and puts to memory a value. This memory value is located with STACK[0] as byte destination offset in memory where the result will be copied, STACK[1] as byte offset from calldata to copy and STACK[2] as byte size to copy from calldata offset.

- CREATE creates a new contract and puts its address on top of the stack. This address is calculated from STACK[0] as the value in wei to send, STACK[1] as byte offset from where you want to start copy the new contract's code from the memory and STACK[2] as byte size of instruction to copy starting from the memory offset.

- EXTCODESIZE will get the byte size of the code given by the address on top of the stack.

To pass the puzzle we need that the JUMPI instruction jumps to JUMPDEST. In order to achieve this, the equality must be met. This means, that the codesize of the recently created contract must be 1B. To do the trick, we need to understand that the input calldata we are passing in, will be the bytecode that the created contract will execute.

There are several ways we could make the created smart contract return 1B as EXTCODESIZE. We are going to use RETURN opcode to make this possible:

- RETURN will pop 2 values from top of the stack to use them as input. STACK[0] will be the memory offset from where to start to read and STACK[1] memory size in bytes to read and return.

Whatever it's in memory, we want to return only 1 instruction that is 1 byte. Our goal is to execute RETURN(offset=0, size=1).

## Detailed explanation

CALLDATASIZE gets the byte size sent from tx in calldata and puts it in front of the stack. We are going to use 0x60016000f3 to solve the puzzle.

Stack before CALLDATASIZE

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

Stack after CALLDATASIZE

```
[5 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

PUSH1 will push value 0 on top of the stack.

Stack after PUSH1

```
[0 5 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

DUP1 instruction will duplicate the value from STACK[0] and put it on STACK[1].

Stack after DUP1

```
[0 0 5 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

CALLDATACOPY will grab the the third upper elements of the stack, and put to memory a value. This memory value is calculated from STACK[0] as byte destination offset in memory where the result will be copied, STACK[1] as byte offset in calldata to copy and STACK[2] as byte size to copy.

Stack and memory after CALLDATACOPY

```
[0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
{60016000f3000000000000000000000000000000000000000000000000000000}
```

Stack and memory after calling CALLDATASIZE again

```
[5 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
{60016000f3000000000000000000000000000000000000000000000000000000}
```

2x PUSH1 will push value 0 two times on top of the stack.

Stack and memory after 2x PUSH1

```
[0 0 5 0 0 0 0 0 0 0 0 0 0 0 0 0]
{60016000f3000000000000000000000000000000000000000000000000000000}
```

CREATE will pop 3 values from stack and put the created address on top of the stack. This address is calculated from STACK[0] as the value in wei to send, STACK[1] as byte offset from where you want to start copy the new contract's code from the memory and STACK[2] as byte size of instruction to copy starting from the memory offset.

This means that the contract created will receive 0 ether and will execute a 5 byte instruction from offset 0 (first element from left to right). 6 is offset[0]. If you count 5 bytes from offset[0], you are grabbing the 5B instruction that was sent as call data value. Lets see this graphically:

```
{ |60016000f3| 000000000000000000000000000000000000000000000000000000 }
```

But wait, what is 60016000f3? It is the bytecode that created contract will execute. Remember our goal is to execute RETURN(offset=0, size=1).

```
0x60016000f3 bytecode is equal to the following mnemonic evm code:
00 6001 PUSH1 01
02 6000 PUSH1 00
04 F3   RETURN
```

After the both pushes, our CONTRACT STACK will be the following:

```
[0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

In this way, RETURN will pop 2 values from the stack to use them as input. STACK[0] as memory offset from where to start to read and STACK[1] memory size in bytes to read and return. What we have in memory is the bytecode 0x60016000f3.

This means that return will read and return the value represented by the first byte from offset 0 (first element from left to right). 6 is offset[0]. If you count 1B from offset[0], you are grabbing the 6001 instruction that will return the value 1. Let's see this graphically:

Contract memory:

```
{ |6001| 6000f3000000000000000000000000000000000000000000000000000000}
```

If we come back to our main code, after CREATE, our stack will be the following:

```
[address 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

EXTCODESIZE will get the byte size of the code given by the address on top of the stack.

Stack after EXTCODESIZE

```
[1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

PUSH1 will push value 1 on top of the stack.

Stack after PUSH1

```
[1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

EQ will compare first to elements of the stack, and put the result of STACK[0] == STACK[1] on top of the stack

Stack after EQ

```
[1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

PUSH1 will push value 0x13 on top of the stack.

Stack after PUSH1

```
[0x13 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0]
```

JUMPI instruction will ONLY JUMP to the instruction given by the value on the top of the stack if the second element of the stack is different than 0 (STACK[1] != 0 ? JUMP STACK[0] : execute next line).

JUMPI will read the value at STACK[1] = 1 != 0, so it will JUMP to STACK[0] = 0x13 instruction, in other words, JUMPDEST.

So if we send the CALLDATA value 0x60016000f3, the program will do the following:

```
00 36 CALLDATASIZE
01 6000 PUSH1 00
03 80 DUP1
04 37 CALLDATACOPY
05 36 CALLDATASIZE
06 6000 PUSH1 00
08 6000 PUSH1 00
0A F0 CREATE
0B 3B EXTCODESIZE
0C 6001 PUSH1 01
0E 14 EQ
0F 6013 PUSH1 13
11 57 JUMPI
13 5B JUMPDEST
14 00 STOP
```
