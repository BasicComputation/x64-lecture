# Logical and shift instructions

## Logical instructions
Is about manipulation of bits in a size according to what the bits are in another value.

- AND - gives 1 if two corresponding bits are 1, 0 otherwise. <br>
- OR - gives 1 if one or both corresponding bits are 1, 0 otherwise. <br>
- XOR - (exclusive or) gives 1 if only one of the corresponding bits are 1. <br>
- NOT - inverts bits in a value

Note: These instruction modify the zero and sign flag, and clears overflow and carry flag.

### Example
In C++: <br>
'&' is AND, '|' is OR, '^' is XOR, '~' is NOT
```
  AND      OR       XOR     NOT
  1100     1100     1100
& 1001   | 1001   ^ 1001  ~ 1100
= 1000   = 1101   = 0101  = 0011
```
XOR can be seen as a comparator, it gives 0 for each bit that are equal. <br>
XOR can also be used to toggle/invert bits: 1 XOR a - if a=0 then it becomes 1, if a=1 it becomes 0.

## Shift instructions
Shifting is about moving bits to the left or right in data of a size. <br>
Shifting 1 to the left, can be seen as doubling the value, as a bits value doubles for each step to the left.
Shifting 1 to the right, can be seen as halving the value.

- SHL - shift left. <br>
  Move bits to the left by a number of times. <br>
  Zero's are inserted from the right, and bits shifted out to left are discarded.
  Except the last bit shifted out is stored in the carry flag.  
- SHR - shift right. <br>
  Move bits to the right by a number of times. <br>
  Zero's are inserted from the left and last bit shifted out to the right is found in the carry flag.
- SAR - shift arithmetic right. <br>
  The same as SHR except the most significant bit (sign bit) is copied when shifting. <br>
  For example 1000(-8) SAR 2 = 1110 (-2). So (-8 / 2) / 2 <br>
  Signed value can be halved a number of times, performing division by the power of 2.

## Assembly example
```asm
and al, F0h       ; First 4 bits in al will become 0.
or bl, al         ; All set bits in al will set the same bits in bl.
xor cl, 10000000b ; bit nr 7 in cl will become 1 if not 1, or become 0 if it is 1
xor eax, eax      ; same bit pattern XOR'ed together gives 0
not dl

mov al, 10010000b ; al = 144 (unsigned)
shr al, 3         ; al will be 00010010 (18) so 144/(2*2*2)
shl al, 1         ; al will be 00100100 (36) so 18*2

mov cl, 4         ; cl can be used as a variable for how far to shift
mov al, 10000000b ; al = -128
sar al, cl        ; al will be 11111000 (-8), so -128/(2*2*2*2)
```

## Other related instructions
TEST instruction performes a AND operation without storing the result, setting zero and sign flag. <br>
```asm
test al, 0Fh      ; are 4 first bits 0 in al? if yes zero flag is set to 1, else 0.
jz aligned

test rax, rax    ; if rax is 0, the zero flag is set to 1. If rax is negative, the sign flag is set to 1.
```

Bit test instructions: <br>
- BT reg/mem, n - Bit Test - test bit nr 'n', set the carry flag equal to the value in bit.
- BTR reg/mem, n - Bit Test and Reset - test bit nr 'n', set carry flag equal to bit value, and clear the bit nr 'n' to 0.
- BTS reg/mem, n - Bit Test and Set - test bit nr 'n', set carry flag equal to bit value, and set the bit nr 'n' to 1.
- BTC reg/mem, n - Bit Test and Complement (invert) - test bit nr 'n', set carry flag equal to bit value, and complement bit nr 'n'.

You also have rotate instructions:<br>
ROL, ROR, RCL, RCR

## More
Bit operations and shifts can be used in many different way. <br>
When you know C/C++ language you can check out this page: <br>
Bit Twiddling Hacks - https://graphics.stanford.edu/~seander/bithacks.html
