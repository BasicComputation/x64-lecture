# Logical and shift instructions

## Logical instructions
is about manipulation of bits in a size according to what the bits are in another value.

- AND - gives 1 if two corresponding bits are 1, 0 otherwise. <br>
- OR - gives 1 if one or both corresponding bits are 1, 0 otherwise. <br>
- XOR - (exclusive or) gives 1 if only one of the corresponding bits are 1. <br>
- NOT - inverts bits in a value

### Example
In C++   & is AND, | is OR, ^ is XOR, ~ is NOT
```
  AND      OR       XOR     NOT
  1100     1100     1100
& 1001   | 1001   ^ 1001  ~ 1100
= 1000   = 1101   = 0101  = 0011
```
XOR can be seen as a comparator, it gives 0 for each bit that are equal.

## Shift instructions
is about moving bits to the left or right in data of a size. <br>
Shifting 1 to the left, can be seen as doubling the value, as a bits value doubles for each step to the left.
Shifting 1 to the right, can be seen as halving the value.


- SHL - shift left. Move bits to the left by a number of times. <br>
Zero's is inserted from the left, and bits to the right are discarded.
Except the last bit out, is stored in the carry flag.  
- SHR - shift right. <br>
Opposite as shift left. Last bit shifted out is stored in the carry flag.
- SAR - shift arithmetic right. <br>
The most significant bit (sign bit) is copied when shifting. Otherwise the same as the others.
This means that a negative value can be halved. For example 1000(-8) SAR 1 = 1100 (-4)

## Assembly example
```asm
and al, F0h
or bl, al
xor cl, 10000000b ; bit 7 will become 1 if not 1, or become 0 if it is 1
not dl

mov al, 10010000b ; al = 144
shr al, 3         ; al will be 00010010 (18) so 144/(2*2*2)
shl al, 1         ; al will be 00100100 (36) so 18*2

mov cl, 4         ; cl can be used as a variable for how far to shift
mov al, 10000000b ; al = -128
sar al, cl        ; al will be 11111000 (-8), so -128/(2*2*2*2)
```

## More
Bit operations and shifts can be used in many different way. <br>
When you know C/C++ language you can check out this site: <br>
Bit Twiddling Hacks - https://graphics.stanford.edu/~seander/bithacks.html
