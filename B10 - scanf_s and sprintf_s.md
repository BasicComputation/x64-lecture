# scanf_s and sprintf_s
scanf_s and sprintf_s uses the same format specifiers as printf.

### scanf_s
scanf_s prompt you for input, and convert text input according to the format specifier, storing the result at addresses provided. <br>
```C++
int scanf_s(
   const char *format [,
   argument]...
);
```
First argument is address to string with format specifiers, can be more than one specifier. Next arguments are addresses to storage of result.
If format specifier is %s (string) or %c (character) you have to provide after the address of buffer, the size of buffer as next argument. 
Multiple reads can be requested if you want. <br>
https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/scanf-s-scanf-s-l-wscanf-s-wscanf-s-l?view=msvc-170


### sprintf_s
sprintf_s convert values to text, storing combined result from address provided as string. <br>
```C++
int sprintf_s(
   char *buffer,
   size_t sizeOfBuffer,
   const char *format,
   ...
);
```
First argument is address to buffer, second is size of buffer, third address of the format string, then the values or addresses as arguments. <br>
https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/sprintf-s-sprintf-s-l-swprintf-s-swprintf-s-l?view=msvc-170

### Example
```asm
includelib legacy_stdio_definitions.lib
includelib ucrt.lib

; https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/scanf-s-scanf-s-l-wscanf-s-wscanf-s-l?view=msvc-170
; https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/sprintf-s-sprintf-s-l-swprintf-s-swprintf-s-l?view=msvc-170

printf proto
scanf_s proto
sprintf_s proto


.code
mainCRTStartup proc
	sub rsp, 28h + 10h

	num1 equ qword ptr [rsp + 20h]


	; scanf_s: get input, convert input according to format specyfier string
	lea rcx, [scanf_fmt]			; format specifiers: %lli = signed 64 bit value
	lea rdx, num1					; where to store result
	call scanf_s

	;sprintf_s: convert values to text store it as string at a buffer
	lea rcx, [sprintf_s_buffer]		; buffer pointer
	mov edx, 64						; buffer size
	lea r8, [sprintf_s_fmt]			; format specifiers: %lli = signed 64 bit value
	mov r9, num1					; first value to be converted
	call sprintf_s

	; printf
	lea rcx, [printf_fmt]			; %s = pointer to string
	lea rdx, [sprintf_s_buffer]		; pointer to what was converted by sprintf_s
	call printf


	xor eax, eax
	
	add rsp, 28h + 10h
	ret
mainCRTStartup endp


.data
	scanf_fmt db '%lli',0

	sprintf_s_fmt db 'Number is: %lli', 0

	printf_fmt db '%s',10,0

.data?
	sprintf_s_buffer db 64 dup (?)
end
```
