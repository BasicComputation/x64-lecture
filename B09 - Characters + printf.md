# Characters
There are several sets of characters, some are 8 bit, 16 bit or 32 bit. <br>

8 bit: Ascii codes, OEM extended ascii and ANSI extended ascii - https://cplusplus.com/doc/ascii/?kw=ascii <br>
16 bit: Unicode - called wide characters in Windows - https://en.wikipedia.org/wiki/List_of_Unicode_characters

Command prompt uses 8bit ascii (with a version of OEM extended ascii) or 16 bit Unicode, . <br>
NOTE: You should not mix 8 bit and 16 bit characters when printing out to command prompt

To print a new line you provide the value 10, tab is 9.

## Example of printing out the 8 bit character set for command prompt using printf
Printf takes in a string as first argument, that string can contain what is called format specifiers: %s %i %x %llu... <br>
For each of the format specifiers, printf convert the data type, like 32 bit signed value, float and so on into text, and replaces <br>
its format specifiers in the string provided with the resulting conversion, and prints out to console. <br>
For each format specifier you provide values or pointers as additional arguments. <br>
https://cplusplus.com/reference/cstdio/printf/ <br>

**Examples:** <br>
%s = pointer to string (insert text in string in the string provided) <br>
%c = a single character <br>
%i = signed integer (32 bit) <br>
%u = unsigned integer (32 bit) <br>
%hi = signed integer (h = length specifier: 16 bits) <br>
%lli = signed integer (ll = length specifier: 64 bit, i = signed integer) <br>
%llu = unsigned integer (ll = length specifier: 64 bit)<br>
%X = unsigned  integer as hexadecimal (32 bit, in uppercase) <br>
%hx = unsigned integer as hexadecimal (h = length specifier: 16 bit, in lowercase) <br>
%p = address printed in hexadecimal (includes preceeding 0's) <br>
%f = double precision floating point (cannot convert 32 bit floats, and values are passed in general purpose registers)

**A format specifier follows this prototype:** <br>
%[flags][width][.precision][length]specifier <br>
see also: https://learn.microsoft.com/en-us/cpp/c-runtime-library/format-specification-syntax-printf-and-wprintf-functions?view=msvc-170

**Examples:**<br>
%-32p = print out address, 32 = minimum 32 characters, - flag means left justified <br>
%20.2f = print out double, 20 = minimum 20 characters (right justified), .2 = maximum 2 decimals


**REMEMBER:** <br>
RBX, RBP, RDI, RSI, R12-R15, and XMM6-XMM15 are returned unchanged by a function. <br >
So these register are usful for temporary storage when calling a function 

```asm
includelib legacy_stdio_definitions.lib
includelib ucrt.lib


printf proto


.code
mainCRTStartup proc
	sub rsp, 28h


	mov bl, 20h				; character to be printed out. ('B' register are unchanged after calling a DLL function)
	mov dil, 0				; counter for how many prints have occured ('DI' register are unchanged after call a DLL function)
	
@1:	cmp dil, 16				; print a new line if counter is 16
	jne @f

	lea rcx, [printf_NL]	; string with new line
	call printf
	mov dil, 0				; reset counter

@@:	lea rcx, [printf_fmt]	; see string and comment below
	mov dl, bl				; dl = character to be presented as hexadecimal value
	mov r8b, bl				; r8b = character to be printed out
	call printf

	inc dil					; increment counter
	inc bl					; next character
	jnz @1					; end of list ?

	xor eax, eax

	add rsp, 28h
	ret
mainCRTStartup endp


.data
	printf_fmt db '%hhX: %c',9 ,0		; print out hexadecimal value of 8 bit arg1 , print out character in arg2 , tab , 0
	printf_NL db 10, 0				; new line

end
```


