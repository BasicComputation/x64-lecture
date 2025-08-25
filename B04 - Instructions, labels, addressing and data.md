# Instructions, labels, addressing and data
In order to learn you have to test things. <br>

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
| Procedure and stack | CALL RET , ENTER LEAVE , POP PUSH |
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
Some instructions are more restricted, so you have to refer to the manual some times.
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
	add [someData], 10		; someData is of size word, so add the 16 bit value 10 with
	add byte ptr [rax], 20	; rax dont show the size of destination, so you have to specify it
	add word ptr [rax], 30
	add dword ptr [rax], 40
	add qword ptr [rax], 50

	; SUB
	sub bl, [r15 + r12]		; bl = bl + 8 bit read
	sub [rdi + rcx*8], rax	; 1. read from memory location sub the value with eax, then store it back
	sub dword ptr [eax], 100

	; CMP
	cmp eax, 10
	cmp rdi, rsi
	cmp [someData], 1
	cmp qword ptr [rax], 0
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
	dec rax
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
div32 struct
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

.data?
	align 4
		divArray div32 8 dup({?,?})		; create array of 8 named "divArray" of data structure "div32" 


	align 16
		buffer:				; label to memory without a data type
			db 100 dup(?)	; reserve 100 bytes


.code

mainCRTStartup proc
	; accessing data structure
	mov [divA].quotient, eax
	mov [divA].remainder, edx
	; or this way
	mov [divA.quotient], eax
	mov [divA.remainder], edx

	; SIZEOF() gives the number of bytes a data type occupies
	mov [divArray + SIZEOF(div32)*0].quotient, eax
	mov [divArray + SIZEOF(div32)*1].quotient, eax

	; addressing with registers
	lea rdi, [divArray]
	mov rcx, sizeof(div32)*4

	mov [rdi + rcx].div32.remainder, eax
	; or this way
	mov [rdi + rcx + div32.remainder], eax

	ret
mainCRTStartup endp

end
```
