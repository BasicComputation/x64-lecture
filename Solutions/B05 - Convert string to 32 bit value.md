# Convert string to 32 bit value
Function cvt_str2int convert a number in a string to 32 bit signed value. <br>
Function return 1 when succesful, storing result at provided address.

Invalid numbers:
- starts with nothing
- starts with '-' then nothing or then '0'
- Should you allow a number that starts with 0's?

You can modify this function to take in the length of number from position in text. <br>
That makes it more useful.

```asm
.data
str1 db '0',0

.code

mainCRTStartup proc
	sub rsp, 8

	lea rcx, [str1]			; address of string
	lea rdx, [rsp]			; address for storing the result
	call cvt_str2int
	cmp al, 1
	cmove eax, [rsp]		; mov result into eax if succesful

	add rsp, 8
	ret
mainCRTStartup endp


cvt_str2int proc
	; rcx = address of string
	; rdx = address for storing 32 bit signed result
	; 8 bit return value: 1 valid, 0 invalid.

	xor ebx, ebx				; ebx = resulting number

	; negative number ?
	cmp byte ptr [rcx], '-'
	sete r15b					; r15b = 1 if '-', else 0
	jne @f
	inc rcx

	; special cases check
@@:	movzx eax, byte ptr [rcx]	; move zero extend
	cmp al, 0					; check if number part of string begins with value 0, if so invalid
	je invalid

	cmp al, '0'					; check if number start with '0'
	jne @f						; if not start converting
	cmp byte ptr [rcx + 1], 0	; is next a terminating character ?
	jne invalid					; if not,invalid number
	jmp check					; go to check
	
	; convert algorith
@@:	cmp al, 0					; end of string ?
	je check
	sub al, '0'
	cmp al, 10
	jae invalid					; is value 0 to 9 ?

	imul ebx, 10				; result = result * 10
	jo invalid					; too large ?
	add ebx, eax				; result = result + value
	jo invalid					; too large ?
	
	inc rcx
	movzx eax, byte ptr [rcx]	; mov zero extend 8 bit value to 32 bit value
	jmp @b

	; is number negative ?
check:	
	cmp r15b, 1					; negative ?
	jne @f
		test ebx, ebx			; set sign and zero flags
		jz invalid				; -0 is invalid
		neg ebx

@@: mov [rdx], ebx				; store result
	mov al, 1
	ret

invalid:
	mov al, 0
	ret
cvt_str2int endp


end
```
