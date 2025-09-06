# Instructions, labels, addressing and data
In order to learn you have to test things. <br>

**Note:** <br>
If you want set a register or variable to a value in hexadecimal, and that value <br>
start with A,B,C,D,E of F, then you get an error, but adding a 0 infront fixes it.

Content
1. list of instructions you should know in the end
2. how to label memory to instructions
3. ways of addressing memory
4. data labels and structures

## Instructions
Below are lists of most commonly used general purpose instructions. <br>
Read up on documentation of instructions and test some of them.

The instructions are documented in the AMD manual volume 3, chapter 3
| | |
| --- | --- |
| Arithmetic | ADD SUB , DIV IDIV , MUL IMUL , INC DEC , ADC SBB , NEG |
| Compare and test | CMP TEST |
| Jumps | JMP Jcc |
| Procedure and stack | CALL RET , POP PUSH |
| Convert size | MOVSX MOVSXD MOVZX , CBE CWDE CDQE , CWD CDQ CQO |
| Bit manipulation | AND NOT OR XOR , BT BTC BTR BTS |
| Shift	| SAL/SHL SAR , SHL SHR , RCL RCR , ROL ROR |
| String and loop | CMPS LODS MOVS SCAS STOS , REP REPZ REPNZ , LOOP LOOPE LOOPNE |
| Other	| MOV  LEA  COMVcc  SETcc  XCHG  NOP , BSF  BSR , CLC  STC  CMC  CLD  STD , RDRAND  RDSEED |

## Memory labels
```asm
.code

; all labels within a function are local to that function
mainCRTStartup proc
	
	@@:	nop
		nop
	@@: nop
		jmp @b	; jump to nearest @@ backwards
	
		jmp @f	; jump to nearest @@ forwards
		nop
	@@:
	
	@1: jmp @2
		nop
	@2: jmp @3
		nop
	@3: jmp @1
	
		call anotherFunction
	
	local_label1:
		nop
		jmp local_label1
		call local_function
mainCRTStartup endp

anotherFunction proc
		jmp local_label1
	local_label1:
		ret
anotherFunction endp

local_function:
	nop
	ret

end
```

## Addressing memory
There are many options for addressing. <br>
Some instructions are more restricted, so you have to refer to the manual some times. <br>
**Note**: <br>
Immediate values are maximum signed 32 bits, they are signed extended into 64 bits when used with a 64 bit register or memory location, <br>
exception is mov allows a 64 bit immediate value into register.
```asm
.code

mainCRTStartup proc
	mov al, [-1000]			; read from address: Instruction Pointer + -1000
	mov [100h], ax			; write to address: Instruction Pointer + 100h
	mov al, [namedAddress]	; read from address: Instruction Pointer + offset to "namedAddress"
	
	lea rsi, [someData]		; rsi = Instruction Pointer + offset to "someData"

	; MOV
	; you can change any register you wish
	; but addressing uses 32 or 64 bit registers

	; read RAM
	mov ax, [rsi]
	mov ax, [rsi + 2]
	mov ax, [rsi + rcx]
	mov ax, [rsi + rcx*2]
	mov ax, [rsi + rcx*4]
	mov ax, [rsi + rcx*8]
	mov ax, [rsi + rcx + 2]
	mov ax, [rsi + rcx*2 - 2]
	mov ax, [rsi + rcx*4 + 4]
	mov ax, [rsi + rcx*8 - 16]
	
	; Write RAM
	mov [rsi], ax
	mov [rsi + 2], ax
	mov [rsi + rcx], ax
	mov [rsi + rcx*2], ax
	mov [rsi + rcx*4], ax
	mov [rsi + rcx*8], ax
	mov [rsi + rcx + 2], ax
	mov [rsi + rcx*2 - 2], ax
	mov [rsi + rcx*4 + 4], ax
	mov [rsi + rcx*8 - 16], ax

	; ADD
	add eax, [r8 + 4]		; eax = eax + 32 bit read
	add [r8 + 8], eax		; 1. read from memory location add the value with eax, then store it back
	add [someData], 10		; someData is of size word, so add the 16 bit value with 10
	add byte ptr [rax], 20	; rax dont know the size of destination, so you have to specify it
	add word ptr [rax], 30
	add dword ptr [rax], 40
	add qword ptr [rax], 50

	; SUB
	sub bl, [r15 + r12]		; bl = bl - 8 bit read
	sub [rdi + rcx*8], rax	; read from memory location subtract the value with rax, then store it back
	sub dword ptr [rax], 100

	; CMP
	cmp [someData], 1		; 16 bit comparison as someData point to word sized data
	cmp qword ptr [rax], 0	; 64 bit comparison, note that the immediate is 32 bit, signed extended into 64 bits
	cmp al, [rdi]
	cmp [rsi + 2], bx
	
	; LEA
	; lea can perform calculations, using the addressing you have seen, so not only IP + number
	lea rax, [rax + rcx*8 - 1000]	; 64 bit
	lea ebx, [ecx + edx*2]			; 32 bit

	; Others
	setae byte ptr [rbp + 8]
	test [rax], bl
	test bl, [rax]
	cmove ax, [rsi + rcx*2 + 2]		; cmov only move into register, and not 8 bits
	shl dword ptr [rax], 3
	sar dword ptr [rbx + rcx*4], cl
	neg qword ptr [rbx]
	inc dword ptr [rcx + rdx*4]
	dec byte ptr [rax]
	movsx eax, byte ptr [rcx + 3]			; convert sign byte to signed dword (move sign extend)
	movsxd rax, dword ptr [rcx + rdx]		; convert signed 32 bit to 64 bit (move sign extend double)
	movzx eax, word ptr [rbx + r8*2 + 4]	; convert unsigned word to unsigned dword (move zero extend)
mainCRTStartup endp


.data
	namedAddress db 10

align 2		; align data by 2. You have align by 4, 8, 16
	someData dw 1,2,3,4

end
```

## Data labeling and data structures
```asm
; (code section is below, the order doesnt matter)

; define data structure named "div32"
div32 struct 4			; number 4 means that structure will aligne next data by 4
	quotient dd ?
	remainder dd ?
div32 ends


.data

		bytes db 1,1Fh

	align 2
		words dw -4, 1001001b

	align 4
		dwords dd 10, 3

	align 8
		qwords dq -1,-2

	align 4
		divA div32 {?,?}	; create "div32" structure named "divA", data members initialized with ?,?
		divB div32 <1,2>	; alternative way to specify initializer list
		divC div32 <>		; same as {?,?,...}

.data?
	align 4
		divArray1 div32 8 dup({?,?})		; reserve memory for an array of 8 "div32" data structures, beginning address is named "divArray"
		divArray2 div32 4 dup(<>)			; same, but alternative way to initialize structure


	align 16
		buffer:				; label to memory without a data type
			db 100 dup(?)	; reserve 100 bytes


.code

mainCRTStartup proc
	; accessing data structure
	mov [divA].quotient, eax		; IP + offset to divA + offset to quotient (0)
	mov [divA].remainder, edx		; IP + offset to divA + offset to remainder (4)
	; or this way
	mov [divA.quotient], eax
	mov [divA.remainder], edx

	; SIZEOF gives the number of bytes a data type occupies
	mov [divArray1 + SIZEOF div32 * 0].quotient, eax
	mov [divArray1 + SIZEOF div32 * 1].quotient, eax

	; Alternative way of accessing array, [offset] where offset is number of bytes
	lea rax, [divArray1 [SIZEOF div32 * 1]]
	mov eax, [divArray1] [SIZEOF div32 * 2].remainder


	; addressing with registers
	lea rdi, [divArray1]
	mov rcx, 2

	mov [rdi + rcx*8].div32.remainder, eax
	; or this way
	mov [rdi + rcx*8 + div32.remainder], eax

	; LENGTHOF gives the number of elements in array
	mov eax, LENGTHOF divArray2

	ret
mainCRTStartup endp

end
```
