# Addition with bits
Two binary numbers can be added together, but in a computer they have to be of the same size, <br>
and the result will be of the same size. Surplus bit of result is discarded, but as I will get into later, <br>
that surplus bit sets a bit in a register in the CPU called the flag register (registers is memory in the CPU). <br>
Individual bits in the flags register is called a flag. The flag in question is called the carry flag.

Addition with bits is the same procedure as with decimal numbers, two corresponding bits with value 1 added toghether <br>
will give a carry that is added together with the next bits in the number.

1+1 = 10 (2+0) <br>
1+1+1 = 11 (2+1)

### 4 bit addition examples
|  |  |
| ---: | :--- |
| 0100 | 4 |
| + 0101 | 5 (4+1) |
| = 1001 | 9 (8+1) |

Here the carry flag will be set as a result of the addition
| | |
| ---: | :--- |
| 1000 | 8 |
| + 1000 | 8 |
| = 0000 | 0 |

Here the maximum values of 4 bits is added together, giving a carry. The actual result is 0b11110. <br>
| | |
| ---: | :--- |
| 1111 | 15 or F |
| + 1111 | 15 or F |
| = 1110 | 14 or E |

Try to add with pen and paper some numbers to get familiar with adding bits.
