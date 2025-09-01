# Debugging
Debugging is to halt the execution of a program at specified memory locations, called breakpoints, and inspect content of registers and memory at that point. 
A single instruction or function can be executed at the time, or letting the program run until another break point is hit or the program ends. 
Break points can be set prior to running debug mode, but also while debugging.

This is useful when you need to track down what causes something unexpected to happen. <br>
In our case debugging is useful for learning as you can inspect the result of a instruction in registers and memory.

Here is a step by step guide for basic debugging in Visual Studio. <br>
Copy the code example below and lets get started. <br>
```asm
; see AMD manual for instruction reference - volume 3 chapter 3
.code

mainCRTStartup proc
	lea r8, [numbers]		; get address of data for inspection
							; LEA = Load Effective Address:
							; here LEA will take the instructions pointer and add it with the offset to "numbers"
	; You could copy the value of instruction pointer, you find it in register window,
	; aligned the value down to 1000h and add 2000h, as .code section and .const section in this example takes up 1000h bytes each.

	mov eax, [numbers]		; eax will be 10
	imul [numbers + 4]		; IMUL is signed multiplication
							; edx:eax = eax * [number + 4] so edx:eax = eax * -3
							; Result is of double size, so upper bits are in stored in 'D' register
							; IMUL sets carry and overflow flag to 1 if the result overflowed
	jno @f					; Jump to nearest @@ forward if not overflow 
	mov eax, -1				; return code = -1
	ret

@@:	mov [product], eax		; store the result in RAM

	mov eax, 7FFFFFFFh		; maximum positive value
	mov ebx, 2
	imul ebx				; edx:eax = eax * ebx = 0:FFFFFFFEh
							; The result is negative in eax, so incorrect
							; therby setting the carry and overflow flags to 1
	jno @f
	nop						; no operation instruction
@@:

	call Function1

ret

mainCRTStartup endp


Function1 proc
	mov eax, 10
	ret
Function1 endp


.data

	numbers dd 10, -3
	product dd ?

end
```

## Instructions
To debug you must specify where the program shall halt, in the source code at the leftmost side, press to insert a breakpoint. <br>
You can have as many as you want. <br>
<img width="662" height="67" alt="image" src="https://github.com/user-attachments/assets/2bc0559d-bc6b-4217-a960-241e80fb29af" /> <br>

Make shure you select Release, not Debug, and press green arrow "Local Windows Debugger". <br>
The application is built and started in debug mode. You can see a yellow arrow at the left side over the breakpoint, that is where the execution is halted. <br>
1. Now I want you to go to menu->Debug->Windows and select "Registers". <br>
   This give the current values in the registers. Right click in the window and select flags. <br>
   You can move this window around and resize it. By dragging it to the sides you can dock it where you want. <br>
2. Go to menu->debug->Windows->memory, select one of them. Arrange the windows to your liking. <br>
   In menu of memory window you see "Address", here you can type or paste in an address.<br>
   Also in "columns" select 16, so there are 16 bytes per row.<br>
3. Go to menu->debug, there you see step into (F11), step over (F10).<br>
	"step over" is to execute a single instruction and also a whole function call. <br>
	"step into" is to execute a single instruction, do press F11. <br>
 	The value in register R8 is now colored red, that means the value is different than what it was. <br>
4. So R8 holds now the address of "numbers". Copy the value in R8 and paste it into memory viewer's address, press enter. <br>
  Right click in Memory and select "4 byte integer", since our data is double words (32 bit or 4 bytes) we now see them arranged correctly. <br>
  Right click again in Memory and select "Signed Display". So now you the values in decimal as signed numbers, 10 and -3. <br>
5. Now you can press F11 to step through and see the results in registers. When you get to where the result is stored, <br>
   notice that where memory has been modified is colored red. So you see -30 stored.
6. To let the program run to the next breakpoint or to the end, press the green arrow "continue", or to stop execution press the red square. <br>

## Note about flags
**AMD manual the flags are named like this:** <br>
Carry – CF | Overflow – OF | Sign – SF | Zero – ZF | Auxiliary – AF | Parity – PF | Direction – DF <br>
**Microsoft name flags like this:**	<br>
Carry – CY | Overflow – OV | Sign – PL | Zero – ZR | Auxiliary – AC | Parity – PE | Direction – UP

## More debugging
While debugging you can also go to menu->Windows->Disassembly <br>
There you see the disassembled view of memory, so machine instruction presented in assembly form. <br>
In "Viewing Options":<br>
- select "Show Code bytes" to see the data of each machine instruction
- select "Show Source code" to see the source code along with disassembly
- select "Show address" to see the address of each machine instruction
- select "Show symbol names" to see the names of addresses in disassembly code <br>
Note that when a instruction read or write data, relative addresses are computated and shown within parentheses. <br>
You see a lot of "add byte ptr [rax],al", that is the disassembled machine instruction with the value 00 00.

In the program above, before you execute the RET instruction of mainCRTMain, have dissambly window open. <br>
Also go to menu->Debug->Windows select "Call Stack". Call stack shows which function you are in in an executable or DLL. <br>

Press F11 to execute the RET instruction, then you get a message "kernel32.pdb not included". <br>
This means that there is no source code available for debugger for kernel32.dll.

Select disassembly window, there you see part of kernel32.dll, look in the call stack window, there you see from the top: <br>
"kernel32.dll ! address()" and "ntdll.dll ! address(). Currently the application is in a function in kernel32.dll. <br>
The prior CALL instruction you see in the disassembly window called a function that most likely had a JMP [reg/mem] instruction, where <br>
register or memory location had the address to mainCRTStartup. 

The call stack window show which functions has called which, the kernel32.dll function has been called by a function in ntdll.dll. <br>
If you follow the path you will eventually get into a function in ntdll.dll that has a SYSCALL instruction that finally closes the process.

## Multiplication and division examples + exceptions
Test out debugging code below to learn about the different multiplication and division instructions.
You find out that the program crashes at one point. That is when the result from the division is to large for the destination register (integer overflow exception). <br>
Also try to divide by 0 and see the message given. While you are at it, try writing into or read from address 0, to do that give a register the value 0, and then "mov al, [register]" for example.

```asm
; see AMD manual for instruction reference - volume 3 chapter 3
.code

mainCRTStartup proc

	; -- unsigned multiplication --
	mov eax, 10
	mov ebx, 3
	mul ebx			; edx:eax = eax * ebx 
					;  MUL will set the upper 32 bits of the 64 bit result in edx
					;  - if you multiply 16 bits you get a 32 bit answer -> dx:ax
					;  - if you multiply 64 bits you get a 128 bits answer -> rdx:rax
					;  - if you multiply 8 bits you get a 16 bit answer in ax
					;  carry and overflow flag are set to 1 if the result did not fit 32 bits

	; -- signed multiplication --
	; IMUL have more options than MUL - see the manual

	mov eax, -10
	mov ebx, 3
	imul ebx		; edx:eax = eax * ebx
					; edx will be all ones when result is negative extending the sign, 0 otherwise.

	imul rcx, r8	; rcx = rcx * r8	(Note that edx is not affected in this case)

	imul r9d, ebx, 10		; r9d = ebx * 10	(Note that edx is not affected in this case)


	; -- unsigned division --
	; Division uses the value in 'D' as part of division.
	; It contains the upper bits of number in 'A' register
	; When performing unsigned division 'D' register normally is set to 0
	; Resulting qotient is found in 'A' and remainder in 'D'
	mov eax, 11
	mov ebx, 4
	xor edx, edx		; edx = 0
	div ebx				; eax = edx:eax / ebx -> eax = 2 edx = 3

	mov ax, 105
	mov r8w, 10
	xor edx, edx
	div r8w				; ax = 10 , dx = 5

	; byte division
	; ah:al / reg|mem
	; ah is the upper 8 bits of the lower 16 bits
	mov al, 10
	mov bl, 3
	mov ah, 0
	div bl			; ah:al / bl	al = quotient , ah = remainder
	mov dl, ah
	
	

	; -- signed division --
	mov eax, -14
	mov ebx, 3
	cdq						; sign extend double to quad -> sign of eax is extended into edx
	idiv ebx				; eax = edx:eax / ebx  		eax will be -4 and edx = -2

	; Sign extend into D:A instructions:
	; CWD Sign-extend AX into DX:AX.
	; CDQ Sign-extend EAX into EDX:EAX.
	; CQO Sign-extend RAX into RDX:RAX.
	

	; -- Exception --
	; a #DE (divide error) exception - integer overflow

	mov eax, 0
	mov ebx, 2
	mov edx, 2
	idiv ebx		;  edx:eax = 2:00000000h / 2 = 1:00000000h (overflow as edx is greater than 0)



	xor eax, eax
	ret
mainCRTStartup endp


end
```

