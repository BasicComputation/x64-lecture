# General purpose registers
Here is a overview of the registers used for integer operations.

There are 16 registers considered general purpose in the CPU. <br>
These are mainly used with whole numbers, called integers, and for holding memory addresses. <br>

Registers with names are used by default by certain instructions. <br>
For example the unsigned multiply (MUL) and unsigned division (DIV) instructions, <br>
uses both 'A' and 'D' register.

A, B, C, D, SI, DI, BP, SP, 8, 9, 10, 11, 12, 13, 14, 15 <br>
A: accumulator  |  B: base  |  C: counter  |  D: data  |  SI: source index  |  DI: destination index  |  BP: base pointer  | SP: stack pointer

All these registers are 64 bits, but smaller parts of these can be read from and written to: 8 bits, 16 bits, 32 bits.<br>
The naming of these parts are:

|        |     |     |     |     |     |     |     |     | |
| ---    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
|        | A   | B   | C   | D   | SI  | DI  | BP  | SP  | 8 - 15 |
| 64 bit | rax | rbx | rcx | rdx | rsi | rdi | rbp | rsp | r8 - r15 |
| 32 bit | eax | ebx | ecx | edx | esi | edi | ebp | esp | r8d - r15d |
| 16 bit | ax  | bx  | cx  | dx  | si  | di  | bp  | sp  | r8w - r15w |
| 8 bit  | al  | bl  | cl  | dl  | sil | dil | bpl | spl | r8b - r15b |

Writing to to 8 or 16 bit part of a register does not change the upper bits, but in x64 modifying the lower 32 bits will clear the upper 32 bits.
You can also access upper 8 bits av the lower 16 bits in A,B,C,D registers. The names ar ah, bh, ch, dh.
