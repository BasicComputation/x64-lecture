# Input and output of text
In Windows if you want to ouput text or get input from command prompt, you have to use functions in DLL's. <br>
There are several options:
- kernel32.dll <br>
  Native to Windows, require more coding to get output or input. <br>
  Functions and data types for the command prompt are documented her: (is in the C language) <br>
  https://learn.microsoft.com/en-us/windows/console/console-reference <br>
  GetStdHandle, WriteConsoleA, ReadConsoleA are the functions you need.
- ucrtbase.dll - Contain implementation of the C library standard. <br>
  Comes with Windows, simplifies many operations. <br>
  Windows C library reference: <br>
  https://learn.microsoft.com/en-us/cpp/c-runtime-library/c-run-time-library-reference?view=msvc-170 <br>
  
  Functions for input/output in the C library uses functions from ntdll.dll it seems.<br>
  Output: puts, putc, printf (printf are not implemented as is any more) <br>
  Input: gets (need to use gets_s instead), getc, scanf / scanf_s (scanf / scanf_s are not implemented as is any more) <br>
  
  C library functions for input and output are also documented for example here: <br>
  https://cplusplus.com/reference/cstdio/ <br>

**Note:** <br>
If you want to use functions for formatted data like printf, scanf_s, sprintf_s you have to include:
- legacy_stdio_definitions.lib
- ucrt.lib <br>


## Lets first print out some text using the function puts. I explain puts below. <br>
Further down are some tips for debugging for calling functions.

```asm
; "The Universal CRT (UCRT) contains the functions and globals exported by the standard C99 CRT library"
; https://learn.microsoft.com/en-us/cpp/c-runtime-library/crt-library-features?view=msvc-170

includelib ucrt.lib      ; ucrt.lib file contains information for 'importing' the 'exported' functions and data
                         ; found in ucrtbase.dll

puts proto              ; proto is symbol declaration of symbols external to this file:
                        ; 'puts' will be found in file ucrt.lib,
                        ; thereby setting up executable for importing the dll function 'puts' from ucrtbase.dll
                        ; (The executable will be setup to use this function, only if being used)

.code

mainCRTStartup proc     ; 'mainCRTStartup' is name of a offset in memory.
                        ; 'proc' tells that it is a function, and that the symbol is global

	sub rsp, 28h     ; align stack by 16 by subtracting by 8, and reserve 20h of data for shadow space at the same time.
                     ; 'shadow space' is for functions that is called to save the first 4 arguments:
                     ; rcx, rdx, r8, r9 and or xmm0, xmm1, xmm2, xmm3

	lea rcx, [message]    ; 'message' is found in the .const section
                        ; lea - load effective address:
                        ; rcx = instruction pointer + value. 
                        ; value = offset in memory to 'message' minus the current offset
                        ; (that is the offset from after current instruction)

	call puts     ; indirectly call 'puts'. call location in table where a jmp instruction have been setup.
                  ; '__imp_puts' is the name of location where address of 'puts' is stored.
                  ; In the table: jmp [__imp_puts] = read the address stored at __imp_puts, and then jump to address.
                  ; Could have been setup to be called directly like this: call [__imp_puts] or just call [puts]
                  ; but no, that does not work, because the intermediate code produced are not setup that way...

	xor eax, eax    ; return code.
                  ; xor a number by itself will become 0
                  ; This instruction takes up fewer bytes than loading it with a 32 bit value

	add rsp, 28h    ; restore stackpointer back to where the return address is
	ret             ; returns to 'kernel32.dll' that then return to 'ntdll.dll',
                  ; where you find the system call for closing the process.

mainCRTStartup endp


.const          ; from here is the section for read only data in the file. 
                ; is loaded into a memory segment with read permission.
	message db 'Hello World',0	  ; db = define byte

end
```

### Explanation of puts (short for "print out string", I guess): <br>
```C++
int puts ( const char* str )
```
First you see the return type of puts. int = signed 32 bit value. Return values are found in 'A' register, so eax. <br>
Within parentheses you see the arguments of puts: first the type (const char*) and then the name of the argument (str) <br>
char* means address of data of size byte. The * signify address while char is the data type at the address. <br>
const means here that puts will be restricted in writing to the address.

So how does puts know when to stop printing out characters? <br>
Strings are array of text that ends with the value 0. <br>
The value 0 is the stop sign.

In the documentation: <br>
- On success, a non-negative value is returned. <br>
- On error, the function returns EOF and sets the error indicator (ferror). <br>
EOF is a negative value, so eax will have that value. <br>
"error indicator" is, as I understand it, a variable for the C library that is set to signal a error has occured, <br>
it resides in a data segment belonging to the DLL.


## Debugging tips
When you get to a call to a function, you can press F10 (step over) to execute the whole function at once. <br>

When stepping into (F11) a call to a DLL function, you get some message saying that for example "ucrtbase.pdb not included". <br>
This means there is no source code avialable. To see where the execution is halted, goto menu->Windows select Disassembly. <br>

Also when a call to a function occure, the debugger keep track of which function it currently in, and where it came from. <br>
Goto menu->Windows select "Call Stack". In the call stack window you see list of calls, the one at top is the current. <br>
You will see in the list the name of program or DLL for each call, along with current address or the address after a call. <br>
In the list you can double click at one of the calls to get back. In disassembly window you can insert breakpoints aswell. <br>


## Getting input
In this example I will use the C library function "gets" to get a string of characters. <br>
"gets" is no good, because you can't specify the maximum number of characters you are able to store <br>
from at address provided to gets. Therefore another version of gets is present, called gets_s, where <br>
you also provide the maximum length of the array in memory. Array is a sequence of data of a size.

gets documentation <br>
https://cplusplus.com/reference/cstdio/gets/

gets_s documentation <br>
https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/gets-s-getws-s?view=msvc-170

gets_s is explained after the code. <br>
To confirm that data has indeed been stored from the memory location, add call to puts with the address of buffer, <br>
or inspect memory.

```asm
includelib ucrt.lib

gets_s proto

.code

mainCRTStartup proc
	sub rsp, 28h

	lea rcx, [buffer]      ; in .data? "section"
	mov edx, 32            ; gets_s expect a 64bit value here, but I only set the 32bit part of 'D' register
                           ; If you remember, modifying lower 32bit clear the upper 32 bits in x64
	call gets_s
		
	xor eax, eax
	
	add rsp, 28h
	ret
mainCRTStartup endp

.data    ; section in file loaded into memory segment with read and write permissions
  values dd 1,2,3,4,?,?    ; dd = define dword
                           ; ? is unknown or no value, but means 0 when created in file.

.data?   ; reserve memory in memory segment where .data section is loaded.
         ; Not stored in file, but information about memory size needed for segment will be set in file 
	buffer db 32 dup(?)  ; reserve 32 times ? of size byte

end
```

### Explanation of gets_s 
```C++
char *gets_s(
   char *buffer,
   size_t sizeInCharacters
);
```
The return value from gets_s is a char*, that is the address of a char (byte). <br>
The address returned is the same as provided when succesful. <br>
Return 0 when not succesful <br>

Documentation says: <br>
"Returns buffer if successful. A NULL pointer indicates an error or end-of-file condition. <br>
Use ferror or feof to determine which one has occurred." <br>
So if 0 you have to call functions ferror and feof to determined what happened. <br>
These functions take in a FILE* data type, you can ignore this for now...

The arguments are a "char*", named "buffer", so the address where data is stored. <br>
and data type "size_t" named "sizeInCharacters", so the length of buffer. <br>
size_t is a custom data type, is set to be unsigned 64 bit value in Windows (x64). <br>

The input will be stored as a string at buffer, that is the characters with a zero terminator (value 0) at the end.

In Remarks: <br>
"If buffer is NULL or sizeInCharacters is less than or equal to zero, <br>
or if the buffer is too small to contain the input line and null terminator, <br>
these functions invoke an invalid parameter handler, as described in Parameter validation. <br>
If execution is allowed to continue, these functions return NULL and set errno to ERANGE."

All this means that if the buffer is to small, or you provide the value 0 as length, a software fault will be induced by <br>
gets_s function, thereby terminating the application. You can provide error handling routine, <br>
but it does not allow anything useful.

### Conclusion
avoid gets_s. I just chose this function for the simplicity of use. <br>
Normally when programming (in C++) you will use more useful functions/methods associated with the objects cout and cin, <br> 
along with a string object for storing input. <br>

But eventually all input and output has to go through ntdll.dll or kernel32.dll <br>
