# Signed and unsigned numbers

In computers there is no special minus sign you can add to a number, <br>
but if you say that the most significant bit (upper bit) in a data size is the negative sign, <br>
you will see that it works in arithmetic operations.<br>
Numbers where the most significant bit is considered a sign of negative, is called signed numbers. <br>
Numbers that you consider not signed, are called unsigned.

### 4 bit example
| bin | dec | | bin | dec |
| ---: | --- | ---: | --- | --- |
| 0000 | 0 | | 1000 | -8 |
| 0001 | 1 | | 1001 | -7 |
| 0010 | 2 | | 1010 | -6 |
| 0011 | 3 | | 1011 | -5 |
| 0100 | 4 | | 1100 | -4 |
| 0101 | 5 | | 1101 | -3 |
| 0110 | 6 | | 1110 | -2 |
| 0111 | 7 | | 1111 | -1 |

### Binary subtraction
When you subtract, you have to borrow from a upper bit, and set the other bits: <br>
**01000000 - 00000100 = 00111100** (64 - 4 = 60)  <br>

If you subtract and cannot borrow from a bit, the computer borrow from outside its range. <br>
When this happens in a computer, the carry flag is set. So the carry flag can also be considered a borrow flag <br>
**0000 - 0001 = 1111 (0 - 1 = -1)** <br>

### Overflow
4 bits can hold the values -8 to 7. <br>
Adding 1 to 7 will result in -8, which is a overflow. <br>
Subtracting -8 with 1 will result in 7, which is a overflow.

In the flag register, there is flag called the overflow flag (OF), which is set when a addition or subtraction results in a overflow. <br>
So if you consider the numbers to be signed, this flag tells whether the result did not make sense.

### Addition examples
```
  1110 (-2)     1110 (-2)     1111 (-1)      1001 (-7)
+ 0001 (1)    + 0100 (4)    + 1111 (-1)    + 1111 (-1)
= 1111 (-1)   = 0010 (2)    = 1110 (-2)    = 1000 (-8)

Overflow examples
  0111 (7)      1101 (-3)
+ 0001 (1)    + 1010 (-6)
= 1000 (-8)   = 0111 (7)
```

### Other
Inverting the bits and adding 1 to it gives the opposite value, this called negation. <br>
```
       -5 = 4           4 + 1 = 5
  1: 1011 = 0100  2: 0100 + 1 = 0101

        5 = -6         -6 + 1 = -5
  1: 0101 = 1010  2: 1010 + 1 = 1011
```
Insted of subtracting you can negate the number to subtract with, and the two numbers instead.
```
  0100 (4)     0100 (4)
- 1110 (-2)  + 0010 (2)
= 0110 (6)   = 0110 (6)
```
