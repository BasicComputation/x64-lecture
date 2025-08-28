# More about MASM
Documentation: <br>
https://learn.microsoft.com/sr-cyrl-rs/cpp/assembler/masm/microsoft-macro-assembler-reference?view=msvc-170

Note: <br>
For some strange reason, if you want set a register or variable to a value in hexadecimal, and that value <br>
start with A,B,C,D,E of F, then you get an error, but adding a 0 infront fixes it.

You can do many things in MASM. I only know a few things, here is a example. <br>

```asm
value = 7
setting = 1


IF value GE 10				; is "value" greater or equal to 10
	value = value SHL 2	
ELSEIF value LT 10			; is "value" less than 10
	value = -1*value
ENDIF



.code

mainCRTStartup proc
	register1 equ eax
	variable1 equ dword ptr [rsp - 4]

	mov register1, value
	mov variable1, register1


if setting eq 1			; if setting = 1 add this part
	add register1, 100
elseif setting eq 2		; else if setting = 2 add this part
	add register1, 200
else					; else stop compilation and give error "forced error"
	.err
endif

	ret
mainCRTStartup endp

end
```
