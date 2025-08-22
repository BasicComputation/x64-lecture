# Stack segment
Every program gets a memory segment called the stack by the operating system, it has read and write permissions.

In the CPU you have the general purpose register ‘stack pointer’ (rsp), that is set to point to a address in the stack segment. <br>
The stack segment has a initial size (4kb/a page), but can grow to a given size (1MB for example). <br>
In a EXE file the committed (initial) size and reserved size is given. <br>
Data put in the stack segment is put on top of each other, and freed from the top in order (so to speak). <br>
Data is put at lower and lower addresses in the stack.

The stack pointer register, along with base pointer register, is used by the instructions: <br>
CALL, RET, PUSH, POP, ENTER, LEAVE.

CALL makes the program execute instructions from another place in memory. <br>
The instruction will first subtract the stackpointer with 8 (as an address is 64 bits/8 bytes), <br>
and then store the value of the instruction pointer at that address, before jumping.

RET instruction returns from a CALL, that is it reads 64 bits from address pointed by the RSP, <br>
and then adds 8 to RSP, and then set the instruction pointer to what was read.

This makes it possible to have sequences of instruction that can be called any number of times, <br>
these sequences are called functions. Also the stack is used for temporary storage of data for a function. <br>
This is achieved by subtracting the stack pointer. 

Note: the stack pointer should point to address that is aligned by 16 before calling another function. <br>
Aligned by 16 means that the first 4 bits in a address is 0. <br>
Floating point instructions often require the data it access from memory to be align by 16, or else...<br>

```asm
; How the stack is setup for functions in Windows programs.
; Presume that a function has called function1.
; function1 will call function2. function2 takes in 4 arguments in registers C,D,8,9
; I will go through this later on in more detail, don't worry.

function1:                ; named offset in memory

  push rbp                ; save rbp in the stack.
                          ; PUSH rbp is equal to:
                          ;   sub rsp, 8
                          ;   mov [rsp], rbp

  sub rsp, 40h            ; make room for 64 bytes. 32 bytes for 'shadow space' and 32 bytes for free use.

  lea rbp, [rsp + 20h]    ; rbp will point to the beginning of the 32 bytes that are free to use
                          ; LEA 'Load Effective Address'
                          ; rbp = rsp + 20h (LEA can also get the value of IP + a value: lea rax, [address])

  ; local variables
  mov dword ptr [rbp], 1      ; store the 32 bit value 1 into local memory, rbp is here the pointer
                          ; in assembly you must specify the size of the value with byte,word,dword,qword
  mov [rbp + 4], ecx      ; store the 32 bit value in 'C' register at the next position.
                          ; so offset by 4 as the first variable takes up 4 bytes.
  mov qword ptr [rbp + 8], 3  ; store the 64 bit value 3 
  mov [rbp + 16], rax     ; store the 64 bit value in 'A' register.

  ; arguments for function2
  mov ecx, 1
  mov edx, 2
  mov r8d, 3
  mov r9d, 4
  call function2          ; call address equal to instruction pointer + value. Value is distance in memory to function2

  ; do something with return value of function2, it is in eax.

  add rsp, 40h            ; restore the stack pointer
  pop rbp                 ; opposite of push: 1. mov rbp, [rsp]  2. add rsp, 8
  ret                     ; 1. read value at rsp 2. add rsp, 8  3. set IP to the value read

function2:
  add ecx, edx
  add ecx, r8d
  add ecx, r9d
  mov eax, ecx            ; return value, if any, is by convention in 'A' register
  ret
```
