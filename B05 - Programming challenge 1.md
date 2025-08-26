# Programming challenge
Convert a string of text containing a number into a 32 bit signed value. <br>
Create a function that takes in address of text and address where to store the value, <br>
function should return 1 on success 0 on failure in AL.

Solution are provided in folder "Solutions".

Each character has a value, the numbers 0 to 9 are in sequence: <br>
0 = 30h 1 = 31h 2 = 32h ... 9 = 39h
https://cplusplus.com/doc/ascii/

Some instructions you can use and hints:
```asm
; see AMD manual for instruction reference - volume 3 chapter 3
.code

mainCRTStartup proc

	lea rcx, [number]

	cmp al, '-'      ; cmp al with with value of - character
	sete r14b        ; set byte to 1 if true, 0 otherwise

	inc rcx          ; increment / add 1
	dec edx          ; decrement / sub 1

	sub al, '0'    
	cmp al, 10
	jae invalid

	imul r15d, 10
	jo invalid

	movzx eax, al    ; move zero extend, turn, here, 8 bit unsigned value into 32 bit unsigned value
	add r15d, eax

	neg r15d        ; negate, convert to positive when negative, or negative when positive

mainCRTStartup endp

.data
number db '-1234', 0

end
```
