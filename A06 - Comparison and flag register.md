# Comparison and flag register

The flags in the flag register are set or cleared depending on the result of a operation. <br>
Various instruction give information about its operation this way. <br>
A few instructions use the state in the flag register, that is conditional instructions.

In the flag register there are 4 flags of interest:
- carry flag (CF) - did the addition result in a carry, or subtraction result in a borrow (unsigned) ?
- overflow flag (OF) - did the result overflow (signed) ?
- zero flag (ZF) - is the resulting number zero ?
- sign flag (SF) - is the resulting number negative ?

### Comparison and condition instructions
Comparing two numbers are done by subtracting the two without storing the result. <br>
The flags are set or cleared, and from the flags (and the sign of the numbers compared), <br>
the relation of the first number to the second number can be determined to be: <br>
- equal - if the zero flag is set
- above (unsigned) - if carry/borrow is cleared, then the first number is larger
- below (unsigned) - if carry/borrow is set, then the first number is smaller
- greater (signed) - more complicated when number are considered signed
- less (signed) - ...

### Conditional instructions and condition codes
cc = condition code
- Jcc - jump if condition is true (only relative to the instruction pointer)
- CMOVcc - 'Conditional mov', mov if condition is true (mov only into register, not 8 bit)
- SETcc - set byte to 1 if condition is true, set to 0 otherwise

The conditions are
- E/Z - equal/zero
- B - below (unsigned)
- A - above (unsigned)
- L - less (signed)
- G - greater (signed)
- S - sign (is negative)?
- C - carry
- O - overflow
- A - auxillary (not used, it's a flag)
- P - parity (not used, it's a flag)
- N - not, is combined with the other conditions, so NE is 'not equal'

E can be combined with B,A,L,G. <br>
So AE is above or equal for example.

### Example
```asm
  cmp ax, bx      ; compare the 16 bit portions of 'A' register, with 'B'
  jne -10         ; jump if not equal to IP + (-10)
  cmova ax, bx    ; copy bx into ax if above. So ax = bx if ax < bx
  setc cl         ; set 8 bit portion of 'C' register to 1 if carry flag is set, 0 otherwise.
```
