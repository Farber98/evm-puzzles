## EVM Mnemonic

```
00 38     CODESIZE
01 34     CALLVALUE
02 90     SWAP1
03 11     GT
04 6008   PUSH1 08
06 57     JUMPI
07 FD     REVERT
08 5B     JUMPDEST
09 36     CALLDATASIZE
0A 610003 PUSH2 0003
0D 90     SWAP1
0E 06     MOD
0F 15     ISZERO
10 34     CALLVALUE
11 600A   PUSH1 0A
13 01     ADD
14 57     JUMPI
15 FD     REVERT
16 FD     REVERT
17 FD     REVERT
18 FD     REVERT
19 5B     JUMPDEST
1A 00     STOP
```

## Explanation

Here we have to pass a VALUE < 27, so GT is met and first JUMP is achieved.

Next, we have to found a combination of values that makes CALLDATASIZE % 3 == 0 and VALUE = 19 - A = F.

So, in order to pass the puzzle, we will use VALUE = F = 15 and CALLDATA = 0x000000 (3 bytes size).

## Detailed explanation
