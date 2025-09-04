# More about MASM
Documentation: <br>
https://learn.microsoft.com/cpp/assembler/masm/microsoft-macro-assembler-reference?view=msvc-170

You can do many things in MASM. I know a few things. <br>
Keywords can be in both lower or upper case. <br>

You can have as many of the same sections as you need within a source code file and between files. <br>
So you can have a .const, .data and .data? per function for example.

## Examples

```asm
; Assembly process goes from the top and down,
; but will look for symbols defined after is if not already found

number = 1						; name = value, give names numerical values with value or expression
value = -10		 
value = value + number			; value = 3 + 1

.data
	IF value GE 10				; GE = greater or equal (assemble this part if if true, then skip the rest)
		data1 db value SHL 1
	ELSEIF value EQ 0			; EQ = equal
		data1 db 0
	ELSEIF value LT 0			; LT = less than
		data1 db -value
	ELSE						; else part is assembled if none of the other gave true
		data1 db 255
	ENDIF


setting = 1

.code

	mainCRTStartup PROC

		IF setting EQ 1
			mov al, 10
		ELSEIF setting EQ 2
			mov al, 100
		ELSE
			.err				; .err stops the assembly process and give error message 'forced error'
		ENDIF

		counter EQU rcx			; 'counter' is replaced with rcx wherever it is used
		xor counter, counter
		
		mov al, reference		; refence is defined below

		ret
	mainCRTStartup ENDP

	reference = 100

end
```

## Macros and more
Macros are a block of code that can be assembled when its name is used in code, it can take in arguments. <br>
Arguments provided are considered text, and within the macro all names of arguments are replaced with the text. <br>

**Example:**
```asm
createString MACRO name:REQ, text:REQ	; argname:REQ means that argument is required
	ALIGN 2
	name dw @SizeStr(text) - 2			; @SizeStr get the length of input text, including quotion marks. Therefor -2.
		db text,0						; paste in argument text as is in code
ENDM

.data
	createString msg1, 'a string'
```

**Example:**

