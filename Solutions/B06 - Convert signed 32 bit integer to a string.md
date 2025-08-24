# B06 - Convert signed 32 bit integer to a string
The algorithm you can use involves getting the remainder when dividing by 10. <br>

DIV and IDIV gives result in 'A' register, and remainder in 'D' register. <br>
The remainder will be first digit in a number, so 1234 / 10 will give 123 as result and 4 as remainder. <br>
When you have the remainder, convert it to character by adding with the value of character 0, and then <br>
store the resulting character. Repeat the operation and when result is 0 you reached the end.<br>

Since algorithm does not know how many characters it will end up with and they are found in reverse order, <br>
you should store them in a temporary storage, and when finished copy the characters to destination in order <br>
and terminate the characters by a 0.

The stack can be used for storage, just copy characters beyond the stackframe at lower and lower addresses. <br>

### A trick
In the solution I have chosen not to use division, but a trick involving multiplication. See below. <br>
I have stolen this from the resulting assembly from C++ code. I haven't figured out why it works... <br>
The reason for avoiding division, is that division takes up many clock cycles, multiplication is much faster.
```asm
mov r11d, 66666667h
; edx = eax / 10
imul r11d            ; edx:eax = eax * r11d
sar edx, 2            ; divide by 4
```

### Solution
```asm
cvt_int2str proc
	; rcx = address to store string
	; edx = 32 bit signed integer

	mov eax, edx
	mov r10, -1				; index into stack for temporary storage of result
	mov r11d, 66666667h		; magic number for dividing 32 bit signed number by 10, by means of multiplication

	test eax, eax    ; perform AND without storing result, setting zero and sign flag.
	jns @f          ; is value negative ?
	neg eax
	mov byte ptr [rcx], '-'
	inc rcx

@@:	mov r15d, eax		; save for later, r15 is used for getting the remainder

	; divide 32 bits with 10, result is in edx
	imul r11d
	sar edx,2 

	mov eax, edx

	; get remainder
	imul edx, 10
	sub r15d, edx

	; temporary save value as character
	add r15b, '0'				; remainder + '0'
	mov [rsp + r10], r15b		; temp storage

	; end of number ?
	test eax, eax
	jz @f					; is value 0?
	dec r10					; change index
	jmp @b

	; save characters in correct order at destination
@@: mov al, [rsp + r10]
	mov [rcx], al
	inc rcx
	inc r10
	jnz @b
	mov byte ptr [rcx], 0
	ret

cvt_int2str endp

end
```
