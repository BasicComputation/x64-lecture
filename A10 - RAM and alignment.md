# RAM and alignment
Memory is read from or written to 64 bits, that is 8 bytes, at the time. <br>
When reading or writing to RAM the first 3 bits of address is not used when addressing, so RAM is divided up into 8 bytes/64 bits. <br>
Every address to RAM points to a minimum of 8 bits (byte), so next address up points to the next 8 bits.

For example if a byte is read from RAM at address 00001110b, 64 bits is read from 00001000b, and the CPU will extract the 8 bits <br>
it needs and shift the bits into lowest position ready for the CPU to be used/stored.

Lets say 32 bits is read from a address where parts of the 32 bits is not in the 64 bits read, then a additional read has to be performed
to get the rest, so the CPU has to temporarely store the first read, shift it down, and then combine the next read to get the 32 bits. 

That is not good, so data should be aligned in memory, that is not to cross the 64 bit boundaries. <br>
In practice, double word (32 bit) is stored at a addresses where the first two bits in the address are 0, so 2 dwords can be stored per 64 bit. <br> 
With a word (16 bits) the first bit in address is 0, so 4 words can be stored per 64 bit.

When writing to RAM, for example a byte, all the 64 bits has to be read first and then the 8 bits has to be modified, and the written back. <br>
Perhaps the control logic of RAM is doing that, not the CPU.

Next up, cache memory.
