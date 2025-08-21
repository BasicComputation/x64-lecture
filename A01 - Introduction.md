# Introduction to computers and programming
If you want to learn programming, you should know a few thing about how a computer works. <br>
Here you will get insight into how the CPU and memory works, among other thing.<br>
The programming languages I will teach you are x64 assembly and eventually C++.<br>
These lectures require no prior knowledge.

Assembly instructions are the lowest readable form of instructions, and are specific for a CPU type. <br>
Instruction the CPU reads and execute are called machine instructions. Machine instructions are just numbers, or data. <br>
There is for example assembly intructions for addition, subtraction and copy, they have a source and a destination operand. <br>
For every variation of operands for an assembly instructions, there is a unique machine instruction. <br>
There are tables inside the CPU for each machine instruction, so a machine instruction point to its table. <br>
Those tables contains the steps that the CPU must do to perform the operation.

Some operations: copy, add, subtract, multiply, divide, compare, jumps, bit operations.

Assembly instructions will be converted into machine instructions. <br>
C++ is higher level code that will be converted into assembly code. <br>

## You need
A computer with AMD or Intel CPU. The most common processor types in desktop and laptops.<br>
Windows and the programming environment Visual Studio from Microsoft. <br>
https://visualstudio.microsoft.com/

There are many other tools than Visual Studio available for programming: C++ compilers, assemblers, debuggers. <br>
But Visual Studio contains everything you need.

## Example
x64 Assembly (x64 refer to 64 bit AMD and Intel processors)
~~~asm
; register = memory unit in the CPU
; they are often 64 bits in size

mov eax, 10       ; put the value 10 into the 32 bit part of 'A' register 

mov bx, [1000]    ; read (32 bits) from address IP + 1000, put the value into the 16 bit part of 'B' register
                  ; IP = instruction pointer register, is 64 bits.
                  ; It holds the address of the next instruction to be fetched and executed.

mov [rcx], dl     ; write 8 bits, that is hold in 'D' register, into memory.
                  ; Address is equal to value in 64 bit part of 'C' register

add eax, ebx      ; eax = eax + ebx (32 bit addition)
                  ; eax = 32 bit part of 'A' register, ebx = 32 bit part of 'B' register

sub eax, [100]    ; eax = eax - value read from address [IP + 100]
~~~

C++ is a high level language (some would argue mid level).
~~~C++
void Test(int a, int b, int* ptr)
{
  // 'int' is a 32 bit whole number (which can be negative, called a signed number)
  int c;
  c = a + b;
  c += b;           // c = c + b
  c = a * b;
  c = a / b;
  c = a % b;        // modulus, the remainder of divison
  c = c + a*(a + b)/10;
  *ptr = c;         // write value in 'c' into address hold by 'ptr'
}
~~~

## Resources
"amd64 architecture programmer's manual volumes 1-5" <br>
CPU documentation, search for and download volumes 1, 3 and 4 <br>
https://www.amd.com/

C++ language and C/C++ library documentation <br>
- C++ tutorial, and have documentation for C and C++ libraries<br>
https://cplusplus.com/ <br>
- Complete technical reference about C and C++, and libraries <br>
https://en.cppreference.com/ <br>
- Microsoft’s C++ language documentation<br>
https://learn.microsoft.com/en-us/cpp/cpp/?view=msvc-170

Windows – win32 (Desktop application and DLL documentation) <br>
https://learn.microsoft.com/en-us/windows/win32/ <br>
https://learn.microsoft.com/en-us/windows/win32/apiindex/windows-api-list

Recommended websites for programming<br>
https://www.geeksforgeeks.org/ <br>
https://www.tutorialspoint.com/

Some recommended Youtube channels <br>
https://www.youtube.com/@BroCodez <br>
https://www.youtube.com/@WhatsACreel <br>
https://www.youtube.com/@BitLemonSoftware <br>
https://www.youtube.com/@MikeShah	
