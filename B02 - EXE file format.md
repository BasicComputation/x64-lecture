# EXE file format
A EXE file is divided into sections. The first section is the format, and size is in hexadecimal 400h. <br>

Common sections are 
- .code - also refered to as .text, is for instructions
- .const - also refered to as .rdata, is for read only data
- .data - is for writable data
  
The format contains information about the executable file. <br>
- Offsets of sections in file
- The size of sections and the size of memory segment the section is loaded into.
- The permissions for the memory segments where the sections are load into: read, write, execute <br>
- The offset in memory to the entry point of the program.
- Offset to information about which DLL files and functions are needed (is found in const section). <br>
  Also in memory segment where const section is loaded, the addresses of the functions are stored
- And other information and settings, like is it a console or GUI (Graphical User Interface) application.

The operating system setup a memory segment where the format is loaded into, it has read permission. <br>
It is logical that memory segments for the sections are then setup, and then loaded into them. <br>
The process of loading DLL files into memory or making them available is then started, and addresses of <br>
functions are stored at specified locations, according to the information in the executable file.

```asm
; code section is loaded into memory segment with read and execute permissions
; found at offset 400h in file
.code
db 'code section starts from here',0

	mainCRTStartup proc
		nop				; no operation, machine instruction is 90h
		ret				; machine instruction is C3h
	mainCRTStartup endp


; const section is loaded into memory segment with read permission
; found at offset 600h in file
.const
db 'const section'


; data section is loaded into memory segment with read and write permissions
; found at offset 800h in file
.data
db 'data section'


; data? is reserved memory, part of same memory segment as where data section is loaded, useally
.data?
db ?,?,?,?	; reserve 4 bytes

end
```
