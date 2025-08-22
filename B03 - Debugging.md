# Debugging
Here I teach you basic debugging in Visual Studio. Copy the first piece of code below and lets get started. <br>

To debug you must specify where the program shall halt, in the source code at the leftmost side, press to insert a breakpoint. <br>
You can have as many as you want. <br>
<img width="662" height="67" alt="image" src="https://github.com/user-attachments/assets/2bc0559d-bc6b-4217-a960-241e80fb29af" /> <br>

### Instructions
Make shure you select Release, not Debug, and press green arrow "Local Windows Debugger". <br>
The application is built and started in debug mode. You can see a yellow arrow at the left side over the breakpoint, that is where the execution is halted. <br>
1. Now I want you to go to menu->Debug->Windows and select "Registers". <br>
   This give the current values in the registers. Right click in the window and select flags. <br>
   You can move this window around and resize it. By dragging it to the sides you can dock it where you want. <br>
2. Go to menu->debug->Windows->memory, select one of them. Arrange the windows to your liking. <br>
   In menu of memory window you see "Address", here you can type or paste in an address.<br>
   Also in "columns" select 16, so there are 16 bytes per row.<br>
3. Go to menu->debug, there you see step into (F11), step over (F10).<br>
  To step through, that is execute, a single instruction, press F11 key. <br>
  The value in register R8 is now colored red, that means the value is different than what it was. <br>
  So R8 holds now the address of numbers. Copy the value in R8 and paste it into Memory viewer's address, press enter. <br>
  Right click in Memory and select "4 byte integer", since our data is double words (32 bit / 4 bytes) we now see them arranged correctly. <br>
  Right click again in Memory and select "Signed Display". So now you the values in decimal as signed numbers, 10 and -3. <br>
4. Now you can press F11 to step through and see the results in registers. When you get to where the result is stored, <br>
   notice that where memory has been modified is colored red. So you see -30 stored.
5. To let the program run to the next breakpoint, press the green arrow continue, or to stop execution press the red square. <br>
  
```asm
; see AMD manual for instruction reference - volume 3 chapter 3
.code

mainCRTStartup proc
	lea r8, [numbers]		; get address of data for inspection

	mov eax, [numbers]		; eax will be 10
	imul [numbers + 4]		; edx:eax = eax * [number + 4] so edx:eax = eax * -3
	
	jno @f					; IMUL set carry and overflow flag to 1 if the result overflowed
	xor eax, eax			; return code = 0
	ret

@@:	mov [product], eax		; store the result

	mov eax, 7FFFFFFFh		; maximum positive value
	mov ebx, 2
	imul ebx				; edx:eax = eax * ebx = 0:FFFFFFFEh
							; The result is negative in eax, so incorrect
							; therby setting the carry and overflow flags to 1
	jno @f
	xor eax, eax	
@@:	ret

mainCRTStartup endp


.data

	numbers dd 10, -3
	product dd ?

end
```
### Note about flags
AMD manual the flags are named like this: <br>
Carry – CF | Overflow – OF | Sign – SF | Zero – ZF | Auxiliary – AF | Parity – PF | Direction – DF <br>
Microsoft name flags like this:	<br>
Carry – CY | Overflow – OV | Sign – PL | Zero – ZR | Auxiliary – AC | Parity – PE | Direction – UP


## Multiplication and division examples
Test out debugging this code if you want to and learn about the different multiplication and division instructions.
You find out that the program crashes at one point. That is when the result from the division is to large for the
destination register. Also try to divide by 0 and see the message given. while you are at it, try writing into or read from address 0.
To do that give a register the value 0, and then "mov al, [register]" for example.


```asm
; see AMD manual for instruction reference - volume 3 chapter 3
.code

mainCRTStartup proc
	
	; unfortunately the address of segment where data is, is not visible
	lea r8, [numbers]		; get address of "numbers"

	; - unsigned multiplication -
	mov eax, [numbers]		; eax = 10
	mul [numbers + 4]		
							; edx:eax = eax * [numbers + 4] (that is 3)
							;  carry and overflow flag are set to 1 if the result did not fit 32 bits
							;  MUL will set the upper 32 bits of the 64 bit result in edx
							;  - if you multiply 16 bits you get a 32 bit answer -> dx:ax
							;  - if you multiply 64 bits you get a 128 bits answer -> rdx:rax
							;  - if you multiply 8 bits you get a 16 bit answer in ax

	jnc @f					; did the result fit 32 bits? if yes jump to nearest @@ forward
	mov [product], -1		; store the value -1 as result if it did overflow
	jmp @1					; jump to unnamed label @1
@@: mov [product], eax
@1:
	
	; - signed division -
	; division, like multiplication, uses the value in 'D' register as the upper bits for the value
	; so in 32 bit division its 64 bits / 32 bits. If result is larger than 32 bits a integer overflow exception occures
	; crashing the program
	; also dividing with 0 crashes the program
	mov eax, [numbers + 8]	; get value -14
	cdq						; sign extend double to quad -> sign of eax is extended into edx
	idiv [numbers + 12]		; eax = edx:eax / 3  edx = remainder (so eax will be -4 and edx = -2 )
	mov [quotient], eax
	mov [remainder], edx
	
	; - signed multiplication
	; IMUL have more options than MUL - see the manual
	imul eax, [numbers + 12]	; eax = eax * [number + 12]  -4*3 = -12  (Note that edx is not affected in this case)
	add eax, edx				; -12 + -2 = -14

	mov ebx, 2
	imul ebx				; edx:eax = eax * ebx

	imul r9d, ebx, 10		; r9d = ebx * 10	(Note that edx is not affected in this case)

	; - unsigned division -
	mov ebx, 4
	xor edx, edx			; edx = 0
	div ebx					; eax = edx:eax / ebx , edx = remainder (eax = 3 edx = 2)

	; Hardware fault example
	; a #DE (divide error) exception - integer overflow
	mov edx, 2
	mov eax, 0
	mov ebx, 2
	idiv ebx		;  edx:eax = 2:00000000h / 2 = 1:00000000h (overflow as edx > 0)

	xor eax, eax
	ret
mainCRTStartup endp


.data

	numbers dd 10, 3, -14, 3

	product dd ?

	quotient dd ?
	remainder dd ?

end
```
