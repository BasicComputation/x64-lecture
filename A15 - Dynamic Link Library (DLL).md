# Dynamic Link Library (DLL)
Applications uses other programs called libraries. In Windows they are called DLL’s. (like the file kernel32.dll) <br>
Unlike a executable, DLL’s cannot be run independently, they are there to serve applications. <br>
In a executable file, there is information about which DLL’s it want to be loaded into RAM. <br>
The operating system searches for the DLL’s needed in for example C:\windows\system32\, or in the path of the executable.

DLL’s are loaded into unknown memory location to the application. <br>
DLL’s contains functions that can be called by an application. 

In the executable file, the name of the functions it want to use from a particular DLL, is stored as text <br> 
(along with a number called hint), and room is reserved in memory for storing the addresses of the functions. <br>
So calling a function in a DLL is done by reading the memory location where the address of function is stored.

DLL’s are often used by different processes at the same time, so to avoid loading a DLL several times, <br>
the memory containing the functions are shared between processes.

For each application where a DLL is loaded, there is a startup function in the DLL that will be called (if existing), so the DLL can setup things internally.
DLL’s can also have data segments, and can share access to its data by the same method of sharing functions (by storing the address of data in known memory location).

In a program created with C++ or the assembler MASM, the location where the address of a DLL function is stored is given a another name.
For example the address of function 'malloc' will be stored at __imp_malloc.

The reason for not naming the location where the address of DLL function is stored with the same name as the function, is to me unknown. 
It could simply have been with the same name as the function. But it is what it is.

As you see in the code below, if you want to call 'malloc', it is to a JMP instruction that has its location named 'malloc'.

```asm
mov ecx, 32
call malloc

; tables of jumps that read the address of the DLL function, and then jump to what was read.
malloc: jmp [__imp_malloc]      ; read from location __imp_malloc and set IP to what has been read.
free: jmp [__imp_free]
realloc: jmp [__imp_realloc]

; why not just this?
call [__imp_malloc]
; or name it as is
call [malloc]

```

In Windows, the DLL ntdll.dll is always present for an application, infact it contains the actual starting point of an application, or rather a process. 
So a function in ntdll.dll calls the applications entry function (starting point). <br>
I don't know what is going on in ntdll.dll, it is not documented. <br>

When you call a function in kernel32.dll for example, which is documented, that dll eventually call a function in ntdll.dll <br>
In ntdll.dll you will find the system call instructions, which transfer control over to the kernel of the operating system. <br>
More on this next.


