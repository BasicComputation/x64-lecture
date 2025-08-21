# Heap segment
The heap is another data segment setup by the operating system for an program. 

Windows provides programs/functions that handles request from a program to reserve memory in the heap of a requested size. <br>
The heap segment is used for smaller memory reservations, if requested memory is too large for the heap segment, new pages are allocated.
When memory reserved is no longer needed it can be freed for later use, by calling a free function.

The functions that manages the heap uses tables and other methods to keep track of where memory is reserved or free. <br>
Those functions are found in the file kernel32.dll. Those are HeapAlloc, HeapFree, HeapRealloc. <br>

The C library, which functions are contained in another DLL file, also have heap allocation functions. See malloc, free and realloc.<br>
In Windows these C library function uses the functions in kernel32.dll

C++ have the keywords new, new[], delete and delete[], they call malloc and free in the C library. <br>
These do some extra work that will be discussed when going into C++.

As with the stack segment, heap segment have a committed and reserved size as specified in the executable file.
