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
0B 6000 PUSH1 00
0D 80   DUP1
0E 80   DUP1
0F 80   DUP1
10 80   DUP1
11 94   SWAP5
12 5A   GAS
13 F1   CALL
14 6000 PUSH1 00
16 14   EQ
17 601B PUSH1 1B
19 57   JUMPI
1A FD   REVERT
1B 5B   JUMPDEST
1C 00   STOP
```

## Explanation

- CALLDATASIZE gets the byte size sent from tx in calldata and puts it in front of the stack.

- PUSH1 will push a value on top of the stack.

- DUP1 instruction will duplicate the value from STACK[0] and put it on STACK[1].

- CALLDATACOPY will grab the the third upper elements of the stack, and puts to memory a value. This memory value is located with STACK[0] as byte destination offset in memory where the result will be copied, STACK[1] as byte offset from calldata to copy and STACK[2] as byte size to copy from calldata offset.

- CREATE creates a new contract and puts its address on top of the stack. This address is calculated from STACK[0] as the value in wei to send, STACK[1] as byte offset from where you want to start copy the new contract's code from the memory and STACK[2] as byte size of instruction to copy starting from the memory offset.

- SWAP5 exchanges STACK[0] and STACK[5] items.

- GAS puts remaining gas (after this instruction) on top of the stack.

- CALL message-call into an account. STACK[0] = amount of gas to send to the sub context to execute. STACK[1] = address to call. STACK[2] = value in wei to send. STACK[3] = byte offset in the memory in bytes, the calldata of the sub context STACK[4] = byte size to copy (size of the calldata). STACK[5] = byte offset in the memory in bytes, where to store the return data of the sub context. STACK[6] = byte size to copy (size of the return data). Returns 0 if reverts, 1 otherwise. (top of the stack)

Same idea as before, the calldata value we are passing in is the bytecode that the created smart contract will execute. In this exercise, the trick is to make the call fail, so it returns a 0 value on top of the stack. This 0 value will match the equality condition, making the jump possible.

How we can make the call fail? We could use REVERT or SELFDESTRUCT for example. 0x60FF60005360016000F3

## Detailed explanation

```
00 36   CALLDATASIZE
01 6000 PUSH1 00
03 80   DUP1
04 37   CALLDATACOPY
05 36   CALLDATASIZE
06 6000 PUSH1 00
08 6000 PUSH1 00
0A F0   CREATE
0B 6000 PUSH1 00
0D 80   DUP1
0E 80   DUP1
0F 80   DUP1
10 80   DUP1
11 94   SWAP5
12 5A   GAS
13 F1   CALL
14 6000 PUSH1 00
16 14   EQ
17 601B PUSH1 1B
19 57   JUMPI
1B 5B   JUMPDEST
1C 00   STOP
```
