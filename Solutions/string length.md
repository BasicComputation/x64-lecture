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
							
	jne strToLong			; did scasb give not equal at last pass ?
	sub ecx, max_length		; get negative difference
	not ecx					; NOT will give the positve value - 1
	mov eax, ecx
	ret
strToLong:
	mov eax, -1
	ret
strLength_v2 endp


strLength_v3 proc
	mov rbx, rcx		; rbx holds copy of address provided for getting length
	mov rdx, rcx		; rdx holds aligned address from where to read 8 bytes
	
	and dl, 0F8h		; align rdx
	and cl, 7			; get offset from aligned address to first byte in string
	shl cl, 3			; cl will be used to shift out unwanted bytes, so times 8 get number of bits to shift

	; "magic numbers"
	mov r8, 8080808080808080h
	mov r9, 0101010101010101h

@@:	mov rax, [rdx]		; get 8 bytes from aligned address

	; this part is the trick, a 8 bit portion will become 80h if zero, else 0
	; pattern = (v - 00101010101010101h) & ~v & 08080808080808080h	
	mov r10, rax
	not r10
	sub rax, r9
	and rax, r10
	and rax, r8

	; make unwanted part of pattern 0
	shr rax, cl
	shl rax, cl

	test rax, rax		; does pattern contain 80h ? that is was a 0 found
	jnz @f
	xor cl, cl			; no longer need to clear out part of pattern
	add rdx, 8			; update address to read from
	jmp @b

@@:	bsf rax, rax		; bit scan forward: give position of first set bit, 
						; scans from right to left. 80h = bit nr 7, 8000h = bit nr 15...
	shr rax, 3			; get byte position
	add rdx, rax		; add to aligned address
	sub rdx, rbx		; subtract that address with address provided
	mov rax, rdx		; return length
	ret

strLength_v3 endp


strLength_v4 proc
	mov rdx, rcx
	mov r8, rcx

	and dl, 0E0h	; align address down to 32
	and cl, 1Fh		; get offset to address provided

	vpxor ymm0, ymm0, ymm0		; packed xor: ymm0 becomes 0

@@:	vpcmpeqb ymm1, ymm0, ymmword ptr [rdx]	; compares 32 bytes at once, give FFh per byte if equal
	vpmovmskb rax, ymm1						; extract sign bits, put them in order in rax

	; clear out lower bits of result, only relevant for first comparison
	shr eax, cl
	shl eax, cl

	bsf eax, eax	; find distance to a 0 if any
	jnz @f			; jump if set bit is  present
	
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
