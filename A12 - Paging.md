# Paging
Ram is divided up into parts of 4096 bytes, or 1000h. Every part is called a page. <br>
Access to pages is restricted, the operating system sets up tables in RAM that holds information about which pages a program have access to. 
Every page has a combination of permissions: read/write/execute, and more.

An array of pages with the same permissions are called a memory segment.

An address a program access is most likely not the actual address in RAM. <br>
Addresses are translated into the real address by the information in the page tables. <br>
See ‘virtual memory’.

In the CPU, there is a part called Memory Management Unit (MMU), that handles addressing. <br>
One part of this is the Translation Lookaside Buffer (TLB), this is a cache that holds the page tables, so the MMU can translate addresses fast.

A program get access to memory segments according to the information in the executable file. <br>
Instructions are loaded into a memory segment with read and execute permissions. <br>
Data is loaded into memory segment with only read permission and or another segment with read and write permission. <br>
When setting up memory segments, memory is first cleared to 0.

See executable file format<br>
Windows - PE format (Portable Executable)
Linux - ELF format (Executable and linkable format)
Mac - macho
