# Convert string to 32 bit value
Function cvt_str2int convert a number in a string to 32 bit signed value. <br>
Function return 1 when succesful, storing result at provided address.

Basic algorithm: <br>
1. variable result = 0
2. check first character for '-', set flag accordingly
3. is first character 0 ? - goto 11
4. get character, convert, check. if 0 goto 10 else if not 0-9 goto 11
5. result = result * 10
6. overflow? goto 11
7. result = result + value
8. overflow? goto 11
9. goto step 4
10. check flag, negate if negative. return result
11. return error

You can modify this function to take in the length of number from position in text. <br>
That makes it more useful.

```asm
.data
str1 db '-92683',0

.code

mainCRTStartup proc
	sub rsp, 18h

	lea rcx, [str1]
	lea rdx, [rsp]
	call cvt_str2int
	cmp al, 1
	cmove eax, [rsp]

	add rsp, 18h
	ret
mainCRTStartup endp


cvt_str2int proc
	; rcx = address of string
	; rdx = address for storing 32 bit signed result
	; 8 bit return value: 1 valid, 0 invalid.

	xor ebx, ebx				; ebx = resulting number

	cmp byte ptr [rcx], '-'
	sete r15b					; r15b = 1 if '-', else 0
	jne @f
	inc rcx

@@:	cmp byte ptr [rcx], 0		; check if number part of string begins with value 0
	je invalid

@@:	movzx eax, byte ptr [rcx]	; mov zero extend 8 bit value to 32 bit value
	inc rcx
	cmp al, 0					; end of string ?
	je valid
	sub al, '0'
	cmp al, 10
	jae invalid					; is value 0 to 9 ?

	imul ebx, 10				; result = result * 10
	jo invalid					; too large ?
	add ebx, eax				; result = result + value
	jo invalid					; too large ?
	jmp @b

valid:	
	cmp r15b, 0					; negative ?
	je @f
	neg ebx
@@: mov [rdx], ebx
	mov al, 1
	ret

invalid:
	mov al, 0
	ret
cvt_str2int endp


end
```
