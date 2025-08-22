# EXE file format
A EXE file is divided into sections. The first section is the format, and size is in hexadecimal 400h. <br>
The file format is called Portable Exectuable.

Common sections are 
- .code - also refered to as .text, is for instructions
- .const - also refered to as .rdata, is for read only data
- .data - is for writable data
  
The format contains information about the executable file. <br>
- Offsets of sections in the file
- The size of sections and the size of memory segment the section is loaded into.
- The permissions for the memory segments where the sections are load into: read, write, execute <br>
- The offset in memory to the entry point of the program.
- Offset to information about which DLL files and functions are needed (is found in const section). <br>
  Also in memory segment where const section is loaded, the addresses of the functions are stored
- And other information and settings, like is it a console or GUI (Graphical User Interface) application.

The operating system setup a memory segment where the format is loaded into, it has read permission. <br>
It is logical that the operating system then setup memory segments for the sections according to the information, and then load them. <br>
The process of loading DLL files into memory is then started, and addresses of functions are then stored at <br>
specified locations, according to the information in the executable file. 

### Inspecting a file
Copy the code below. To create the executable without starting it, go to menu->build->build solution. <br>
Make shure Release and not debug version is built.

After the executable is built, right click on the project, go to add->existing item. Find the exe file, click add. <br>
Now the file is listed in the solution explorer. Right click on it, choose 'open with' <br>
scroll down in list and select 'Binary Editor', press ok. <br>

So the numbers you see to the left are the offset into file, in the middle the data, and to the right data represented as text.

Each section in file is aligned by 200h. If you scroll down you see other things, like the path to a .pdb file. <br>
That file contains debug information for the executable, making it possible to debug with source code. <br>
Next up how to debug.

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
