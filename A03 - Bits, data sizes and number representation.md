# Bits, data sizes and number representation
A bit hold the value 0 or 1, that is voltage off or on in a wire or memory cell. <br>
A 64 bit computer operates with up to 64 bit in parallel. <br>
The CPU work mainly with these sizes: <br>
```
8 bit - byte
16 bit - word
32 bit - double word (dword)
64 bit - quad word (qword)
```

### Binary and hexadecimal numbers
A single bit can hold two combinations, adding a bit doubles the number of combinations. <br>
So 2 ^ n, where n is the number of bits, gives the total number of combinations. <br>
Subtracting the total number of combinations with 1, gives the maximum value those bits can hold. <br>

A binary number is numbers represented with bits - 10010001 <br>
The position of the bit determine the value (from right to left): <br>
```
  bit number 0 holds the value 1 (if set to 1)
  bit number 1 holds the value 2
  bit number 2 holds the value 4
  bit number 3 holds the value 8
  bit number 4 holds the value 16
  then 32, 64, 128, 256, 512, 1024, 2048...
```
So the value doubles for each bit added. <br>
The binary number 1100 is equal to 4 + 2 = 6

Hexadecimal numbers are numbers with base 16 - 2A3F1007 <br>
The value 10 is represented by the letter 'A', 11=B 12=C 13=D 14=E 15=F <br>
Hexadecimal numbers are convinient in programming as 4 bits can hold the values 0 to 15, so <br>
8 bits can be split in two and be represented by two hex numbers, 16 bits by 4, 32 bits by 8, 64 bits by 16. <br>
The individual bits can be read out from a hex number. <br>
```
  Binary numbers is written like this: 0b1001011 or 1001011b
  Hexadecimal numbers: 0xF1B or F1Bh
```
**TIP:** <br>
If you open up the windows calculator, you can in the menu choose 'Programmer'. <br>
There you select to write in numbers in decimal, hexadecimal, binary and octal (base 8) <br>
and see the numbers represented in the differnt forms. <br>
You can choose the size as well, qword, dword, word, byte. <br>
Note that numbers are considered signed, can be negative. More on signed numbers later.


**Study the table below**

| decimal | Binary | | Hexadecimal | |
| ---: | ---: | :--- | ---: | :--- |
| 0 | 0000 | | 0 | |
| 1 | 0001 | | 1 | |
| 2 | 0010 | | 2 | |
| 3 | 0011 | 2+1 | 3 | |
| 4 | 0100 | | 4 | |
| 5 | 0101 | 4+1 | 5 | |
| 6 | 0110 | 4+2 | 6 | |
| 7 | 0111 | 4+2+1 | 7 | |
| 8 | 1000 | | 8 | |
| 9 | 1001 | 8+1 | 9 | |
| 10 | 1010 | 8+2 | A | |
| 11 | 1011 | 8+2+1 | B | |
| 12 | 1100 | 8+4 | C | |
| 13 | 1101 | 8+4+1 | D | |
| 14 | 1110 | 8+4+2 | E | |
| 15 | 1111 | 8+4+2+1 | F | |
| 16 | 10000 | | 10 | 1*16 + 0 |
| 20 | 10100 | 16+4 | 14 | 1*16 + 4 |
| 330 | 101001010 | 256+64+8+2 | 14A | 1 * 16*16 + 4 * 16 + 10 |
