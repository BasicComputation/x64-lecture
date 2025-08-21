# CPU kernel and user mode
The CPU can have several modes, that is it can restrict the access to CPU resources (registers) and choose how the CPU behaves (enable paging for example). 
In user mode, which applications run in, access to system registers are denied, while in kernel mode it has access to all registers. 
The kernel of the operating system run in kernel mode, so it has access to all registers, all address space – that is all RAM and hardware.

Applications have only access to memory setup through the page tables.
The operating system choose to not to provide direct access to hardware and its own content for an application.
So to communicate with for example hardware, the operating system has to be in charge and do the work of transferring data, and so on. 

The only way to pass control to the kernel of the operating system is with a system call or interrupt instruction. 
A special register holds the address to a table of addresses. System call instruction are provided with a number in ‘A’ register that tells where
in the table to read the address from, and then execute from that address.
```asm
mov eax, 10
syscall
```
When an application do something illegal, like trying to access memory that is not available, a fault occurs, and control is passed over to the kernel of the operating system.
Special registers hold the entry addresses to execute from for each particular fault. <br>
When a fault occures the application is terminated, unless it has provided error handling routines.


