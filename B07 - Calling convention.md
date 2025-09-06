# Calling convention
**Remember:** The stack pointer should be aligned by 16 when calling another function, this is because many packed floating point instructions require address to be aligned by 16.

## Stack frame
A function that calls other functions sets up a stack frame, you can say a stack frame is memory in the stack belonging to a function.<br> 
It contain first the return address, and then it is up to function to:
1. save basepointer in stack if used. Typically done by instruction PUSH RBP. <br>
   This will also align the stack pointer by 16.
3. Reserve room for local variables (storage for data) by subtracting the stack pointer. <br>
4. Only 4 registers are used to pass arguments to a function in Windows, for more arguments reserve room for additional arguments by subtracting the stack pointer.
	In Windows the convention is that each argument takes up 8 bytes, even if it is only a byte. <br>	
6. In Windows you also subtract the stack pointer by 20h for "shadow space", this is where a called function <br>
  can save arguments passed via registers. So 20h is 4*8 bytes, so 4 register argument can be saved if needed. <br>
7. Lastly set base pointer to point to the first local variable

```asm
func1 proc
	push rbp
	sub rsp, 10h + 10h + 20h				; 10h for local variables, 10h for arguments, 20h for shadow space
	lea rbp, [rsp + 10h + 20h]				; rbp will hold address to local variables

	stackframe equ 10h + 10h + 20h + 10h	; name equ text - wherever you use the name, the text is inserted

	; save arguments, if needed, in shadow space of function that called this function
	; rsp + size of stackframe + offset
	mov [rsp + stackframe + 0], rcx			; arg 1
	mov [rsp + stackframe + 8], rdx			; arg 2
	mov [rsp + stackframe + 10h], r8		; arg 3
	mov [rsp + stackframe + 18h], r9		; arg 4

	; access additional arguments
	mov eax, [rsp + stackframe + 20h + 0]	; arg 5
	mov eax, [rsp + stackframe + 20h + 8]	; arg 6
	mov eax, [rsp + stackframe + 20h + 10h]	; arg 7
	mov eax, [rsp + stackframe + 20h + 18h]	; arg 8

	; call function that takes 6 arguments
	mov ecx, 1
	mov edx, 2
	mov r8d, 3
	mov r9d, 4
	mov dword ptr [rsp + 20h], 5
	mov dword ptr [rsp + 28h], 6
	call otherFunction

	; remove stack frame
	add rsp, 10h + 10h + 20h
	pop rbp
	ret
func1 endp
```

## Calling convention
A calling convention is a standard for how to pass arguments to another function and more, like a contract. <br>
Windows uses the "Microsoft x64 calling convention". <br>
https://learn.microsoft.com/en-us/cpp/build/x64-calling-convention?view=msvc-170

First 4 integer and pointer arguments are passed in registers: C D 8 9 <br>
First 4 Floating point arguments are passed in registers: xmm0 xmm1 xmm2 xmm3 <br>
"Additional arguments are passed on the stack in right-to-left order."<br>
I would say passed from the nearest, as offset to arguments increase by 8 for each addition argument. <br>
So in this convention each argument takes up 8 bytes, even if the argument is smaller.

The convention allow only a total of 4 registers as arguments, <br>
so if you pass both integer and floating point arguments, lets say in this order: <br>
integer, float, float, integer, the registers used are: C , xmm1 , xmm2 , 9 <br>
So registers used is skipped to be equal to the argument number. Another example: <br>
float, float, integer, float: xmm0 , xmm1 , 8 , xmm3

Part of the convention is that certain registers has to be saved before they are modified, so they can be restored before returning. <br>
RBX, RBP, RDI, RSI, R12-R15, and XMM6-XMM15

If you create a function or a DLL to be used by others, you must return with the values in these registers unchanged.

Return value from function is found in 'A' or xmm0 register.
