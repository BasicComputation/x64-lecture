# Basic overview of a computer (64 bit)
The CPU (Central Processing Unit), is connected to graphics card, harddisc controller, USB controller, others, and of course main memory, called RAM (Random Access Memory). <br>

The CPU is connected to the other parts via a data bus (64 bit), address bus (might be 48 bit), and a control bus. <br>
*(a bit holds the value 0 or 1, that is voltage off or on in a wire or memory cell. Several bits are processed in parallel in a computer)*

- The address bus select which location to read from or write to. <br>
- The control bus selects if to read or write, and pass signals. <br>
For example data is now available in the databus, or the write is completed. <br>
- The data bus pass data/numbers between the CPU and the different parts

The CPU directs everything that goes on in a computer. <br>
It reads data for processing, and write data to a location for storage, output or for controlling settings. <br>

### Addressing RAM
Each address into RAM points minimum to 8 bits, 8 bits is called a byte. <br>
So adding 1 to a address will point to the next 8 bits. <br>
Actually 64 bits, that is 8 bytes, is read from or written to RAM at once. <br>
More on RAM and other memory later.

### DMA - Direct Memory Access controller
As said the CPU directs everything, if you for example want to load into RAM a file from the harddrive, the CPU can avoid reading data from the harddrive controller for then to store the data into RAM.
This is where the DMA comes in, it can be programmed to read from a address and store what is read to another address, and do it in chunks. This leaves time for the CPU to do other work. <br>

### Instruction fetching and execution
Inside the CPU there are small memory units called registers, one special register is the 64 bit Instruction Pointer (IP). <br>
It holds the address of the next instruction to be fetched and executed. <br>

Lets say you start an application, the operating system loads the executable file into memory, <br>
and then sets the instruction pointer to the address of the first instruction in the program. <br>
The CPU will read from that address and decode the data read as a instruction (fetching the instruction). <br>
Before executing the instruction, the instruction pointer will be updated to point to the next instruction. <br>

Instructions in x64 Inel/AMD CPU's is of varying size, can be a single byte <br>
and as part of the instruction there might be data, for example a offset into memory. <br>
The raw instructions are called machine instructions, and each of them point into its own table <br>
inside the CPU that contains all the steps the CPU must do for the particular operation.

### Common CPU operations
- Copy - copy data betwen registers and adresses, or between registers. <br>Some registers are used to hold data or addresses, other control modes of the CPU, or contain a state.
- Arithmetic - Addition, subtraction, multiplication, division and modulus, square root. <br> These are basically the arithmetic operations the CPU can do.
- Comparison - Compare numbers. Are they equal, the one larger or smaller? <br> Also test numbers, is it zero or negative, or are certain bits in the data set to 1 or 0.
- Jump - Set the instruction pointer to another value, a absolute or relative address (IP + value). <br> There are also conditional jumps, so jump if equal for example.
- Logical - Manipulate bits in data. You have AND, OR, XOR and NOT operations.
- Shift - move bits to the left or right in data.

### Basic general purpose instructions
The instructions are documented in the AMD manual volume 3, chapter 3
| | |
| --- | --- |
| Arithmetic | ADD SUB , DIV IDIV , MUL IMUL , INC DEC , ADC SBB , NEG |
| Compare and test | CMP TEST |
| Jumps | JMP Jcc |
| Procedure and stack | CALL RET , ENTER LEAVE , POP PUSH |
| Convert size | MOVSX MOVSXD MOVZX , CBE CWDE CDQE , CWD CDQ CQO |
| Bit manipulation | AND NOT OR XOR , BT BTC BTR BTS |
| Shift	| SAL/SHL SAR , SHL SHR , RCL RCR , ROL ROR |
| String and loop | CMPS LODS MOVS SCAS STOS , REP REPZ REPNZ , LOOP LOOPE LOOPNE |
| Other	| MOV  LEA  COMVcc  SETcc  XCHG  NOP , BSF  BSR , CLC  STC  CMC  CLD  STD , RDRAND  RDSEED |


