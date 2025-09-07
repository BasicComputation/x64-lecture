# More about MASM
Documentation: <br>
https://learn.microsoft.com/cpp/assembler/masm/microsoft-macro-assembler-reference?view=msvc-170 <br>

- MASM is case insensitive
- You can have as many of the same sections as you need within a source code file and between files. <br>
So you can have a .const, .data and .data? per function for example. 
- You can define data with BYTE, WORD, DWORD, QWORD, instead of db, dw, dd, dq. <br>
You also have REAL4 and REAL8 for 32 bit and 64 bit floating point data, XMMWORD and YMMWORD are packed

## Some examples

```asm
; Assembly process goes from the top and down,
; but will look for symbols defined after is if not already found,
; so assembler goes through symbols more than once if needed.

number = 1						; name = value, give names numerical values with value or expression
value = -10		 
value = value + number			; value = -10 + 1

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
## Union
An union is a data structure that contains members stored at same address. <br>
Size of union is the size of largest member, alignement can be provided.

Unfortunatly you cannot select which member to initialize in an union, only the first member is initialized.

```asm
U1 UNION 8				; align data after structure by 8
	f64		REAL8	?
	s64		dq		?
	s8		db		?
U1 ENDS


.data
align SIZEOF U1.f64		; align data by size of member of U1
	number U1 <?>

.code

	mainCRTStartup proc
		mov al, [number.s8]
		movq xmm0, [number.f64]
		ret
	mainCRTStartup endp

end
```
## Structures with structures
```asm
operator@plus = 1
operator@minus = 2
operator@multiply = 3 
operator@divide = 4

kind@endof = 0
kind@number = 1
kind@operator = 2

TokenData UNION 8
	number	REAL8	?
	operator db ?
TokenData ENDS

Token STRUCT 8
	data TokenData <>	; union member
	kind db ?
Token ENDS

.data?
align 8
	number Token 64 dup (<>)

.code

	mainCRTStartup proc
		
		mov al, [number.kind + SIZEOF Token * 3]

		; access operator member of union TokenData in Token structure
		mov al, [number.data.operator + SIZEOF Token * 3]
		
		; store values in Token structure
		movq [number.data.number + SIZEOF Token * 2], xmm0
		mov [number.kind + SIZEOF Token * 2], kind@number
	
		ret
	mainCRTStartup endp

end
```

## Macros and more
Macros are a block of code that can be inserted when its name is used in code, it can take in arguments. <br>
Arguments provided are considered text, and within the macro all names of arguments are replaced with the text. <br>

**Example:**
```asm
infoString MACRO name:REQ, text:REQ		; :REQ means that argument is required
	ALIGN 2
	name dw @SizeStr(text) - 2			; @SizeStr get the length of argument text, including quotion marks, therefore -2.
		db text							; paste in argument text as is in code
ENDM

.data
	infoString msg1, 'a string'			; invoke the macro, content of macro is inserted with replacements, ready for assembly.
```

**Example:** <br>
Enumeration of values.
```asm
; EXPLANATION OF MACRO ARGUMENTS
;
; startvalue := <0>
;		default value is provided after := (if not a number provide without <>)
;
; items:VARARG
;		VARARG means that you can provide as many arguments as you wish

enum MACRO name, startvalue:=<0>, items:VARARG

	; LOCAL makes variable local to macro (not accessible on the outside)
	LOCAL count

	count = startvalue	

	; FOR loops through for each argument provided within <>
	; here 'item' will be set to be equal to each argument iterated
	FOR item, <items>

		; @CatStr(A,B) combines text, so A,B will be AB
		@CatStr( @CatStr(name, @), item ) = count

		count = count + 1

	ENDM

ENDM

; \ is to ignore newline
enum type, ,\ 
		endof, number, operator
enum operator, 10,\
		plus, minus, multiply, divide,\
		exponent, log, log10, sinus, cosinus, tangens, \
		leftParenthesis, rightParenthesis

.code

mainCRTStartup PROC
	cmp al, type@operator
	cmp al, operator@plus
	cmp al, operator@minus
	ret
mainCRTStartup ENDP

end
```
