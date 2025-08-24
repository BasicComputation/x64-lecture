# String length
Problem: find first occurance of value 0 of size byte, return length up to the 0.

There are several ways of doing this, I provide 5 potensial solutions here. <br>

C library has a string length function: <br>
https://cplusplus.com/reference/cstring/strlen/

```asm
.data
db 10 dup (?)
str1 db '12345678', 0


.code

mainCRTStartup proc

	lea rcx, [str1]
	call strLength_v4
	ret
mainCRTStartup endp


strLength_v1 proc
	mov rax, rcx
	dec rax
@@:	inc rax
	cmp byte ptr [rax], 0
	jne @b
	sub rax, rcx
	ret
strLength_v1 endp


strLength_v2 proc
	max_length = 0FFFFFFFFh

	mov rdi, rcx
	mov al, 0				; value to be compared: [rdi] == 0 ?
	mov ecx, max_length
	repne scasb				; repne - repeat when not equal
							; scasb - Scan String Byte:  
							; 1. is rcx == 0? if yes, skip the rest of instruction 
							; 2. cmp al, [rdi] 3. inc rdi 4. dec rcx
							
	jne strToLong
	sub ecx, max_length
	not ecx
	mov eax, ecx
	ret
strToLong:
	mov eax, -1
	ret
strLength_v2 endp


strLength_v3 proc
	
	mov rbx, rcx

; byte compare - do this as long as address is not aligned by 8
@1:	test cl, 7				; is address aligned by 8? (value 7 is 0111b, if last 3 bits is 0 then address is aligned by 8)
							; if not, compare a byte at the time
	jz @2

	cmp byte ptr [rcx], 0
	je @f
	inc rcx
	jmp @1

@@:	mov rax, rcx
	sub rax, rbx
	ret

; part of trick to find occurance of 8 bits with value 0 in quadword 
@2:	mov r8, 08080808080808080h
	mov r9, 00101010101010101h

	; get 8 bytes at the time from a aligned address
@3:	mov rax, [rcx]

	; this part is the trick, a 8 bit portion will become 80h if zero, else 0
	; pattern = (v - 00101010101010101h) & ~v & 08080808080808080h
	mov r10, rax
	not r10
	sub rax, r9
	and rax, r10
	and rax, r8
	
	test rax, rax
	jnz @f			; if not zero mean that a 0 was found in quadword
	add rcx, 8
	jmp @3

@@:	bsf rax, rax	; bit scan forward: give position of first set bit, scans from right to left. 80h = bit nr 8, 8000h = bit nr 16...
	shr rax, 3		; divide by 8 - gets byte position of set bit
	sub rcx, rbx	; get offset in memory that was read from
	add rax, rcx	; add with offset to byte containing value 0
	ret

strLength_v3 endp


strLength_v4 proc
	mov rdx, rcx
	mov r8, rcx

	and dl, 0E0h	; align address down to 32
	and cl, 1Fh		; get offset to address provided

	vpxor ymm0, ymm0, ymm0

@@:	vpcmpeqb ymm1, ymm0, ymmword ptr [rdx]	; compares 32 bytes at once
	vpmovmskb rax, ymm1

	; clear out lower bits of result, only relevant for first comparison
	shr eax, cl
	shl eax, cl

	bsf eax, eax	; find distance to a 0 if any
	jnz @f			; jump if set bit is present
	
	xor cl, cl
	add rdx, 32
	jmp @b

@@:	add rdx, rax	; get address of 0
	sub rdx, r8		; get length
	mov rax, rdx
	ret

strLength_v4 endp


strLength_v5 proc
	mov rsi, rcx
	mov eax, 16
	mov edx, 16
	xor ebx, ebx

	; when not aligned, do byte comparison, up to 15. so not good
@1:	test sil, 0Fh
	jz @2

	cmp byte ptr [rsi], 0
	je @f
	inc rsi
	inc rbx
	jmp @1

@@:	mov rax, rbx
	ret

@2:	pxor xmm0, xmm0
	PCMPESTRI xmm0, [rsi], 00001000b
	add rbx, rcx
	add rsi, rcx
	cmp cl, 16
	je @2

	mov rax, rbx
	ret
strLength_v5 endp


end
```
