# Floating point numbers
Floating point numbers are numbers with fractions, like 1.25 <br>
Specialized part of CPU operates with floating point numbers. It has it’s own set of registers and instructions.<br>
Operation are for examples + - * / square root, inverse square root and compare.

There are two sizes for floating point numbers: <br>
Single precision floating point (32 bits) and double precision floating point (64 bits) <br>
Or just ‘float’ and ‘double’

##  Encoding
One bit tells whether the number is positive or negative. <br>
In a float the exponent is 8 bits, fractions are 23 bits, fraction is called significant or mantissa.

First bit in mantissa represent when set, ½, next is ¼, and next 1/8, next 1/16… <br>
That is 2^-1, 2^-2, 2^-3 and so on. <br>
The number 1 is added to the mantissa, so if mantissa is 0.5 (2^-1) it is actually 1 + 0.5 (2^0 + 2^-1) <br>
The exponent bits is a unsigned number, in a float that number is subtracted by the CPU with 127 to get the exponent.

### Example
If resulting exponent is 4, and mantissa is 110100000.... with the implicit one its 1.1101, then to calculate the value: <br>
(2^0 + 2^-1 + 2^-2 + 2^-4) * 2^4 <br>
= 2^(0+4) + 2^(-1+4) + 2^(-2+4) + 2^(-4+4) <br>
= 2^4 + 2^3 + 2^2 + 2^0 <br>
= 16 + 8 + 4 + 1 = 29

If resulting exponent is -2, and mantissa is 101000.... then to calculate the value: <br>
(2^0 + 2^-1 + 2^-3) * 2^-2 <br>
= 2^(0-2) + 2^(-1-2) + 2^(-3-2) <br> 
= 2^-2 + 2^-3 + 2^-5 <br>
= 0.25 + 0.125 + 0.03125 <br>
= 0.40625

Numbers can be very large or small. Cannot represent all possible numbers accuratly because of the fractions used. <br>

Special cases: 
- infinity - the sign bit is set to 0 for positive infinity and 1 for negative infinity,<br>
  the exponent field is all ones, and the mantissa (or fraction) is all zeros.
- NAN - (Not A Number) Result is undefined. There is quite NAN and signaling NAN
- Denormalized - These are very small numbers, smaller than the smallest normalized number. <br>
  They are represented with a special exponent (all 0 bits) but a non-zero fraction
- Zero - all bits are 0, except the sign bit, can be set, giving 0 and -0.

## Register overview
There are 16 or 32 registers for holding floating point values, as well as integers. <br>
MXCSR is register for settings for floating point operations.

## SIMD - Single Instruction Multiple Data
In x64, the unit for floating point operations can hold 4/8/16 floats or 2/4/8 doubles per register (depend on the CPU), and perform operations on two sets of floats or doubles at once.
There are up to 16 or 32 registers. Each of these are up 128/256/512 bits wide. <br>
When refering to the 128 bit portion of a register, it is xmm, when refering to 256 bit portion its ymm, if 512 bits its zmm. <br> 
Modern CPUs support at least 256 bits.

Integer operations are also supported, on several bytes, word or dwords at the same time. <br>
There are all kinds of special operations, like add with saturation, so when adding bytes the result will not exceed the value 255.

## In assembly
```asm
mov eax, 1000
cvtsi2ss xmm0, eax    ; convert the value in eax to a float, store it in the lower 32 bit of 128 bit register xmm0
                      ; cvt = convert , si = scalar integer (32 or 64 bit), ss = scalar single (float) ... sd = scalar double (double)
                      ; scalar means operate with a single number,
                      ; packed means operate with at least 4 floats or 2 double at the same time.

addss xmm0, xmm1      ; xmm0 = xmm0 + xmm1 - add scalar single: add two floats together
cvtss2si eax, xmm0    ; convert scalar single to scalar integer, rounded to nearest
cvttss2si eax, xmm15  ; convert truncated scalar single 2 scalar integer, trunctate the fractions (round down)

addpd xmm2, xmm3      ; add packed double
                      ; 2x double is added with 2x double respectivly
                      ; a0 = a0 + b0 and a1 = a1 + b1
vaddps ymm0, ymm1, ymm2		; ymm0 = ymm1 + ymm2 - add packed singles (256 bits). Two sets of 8x floats are added together.
vaddps xmm0, xmm1, xmm2		; ymm0 = ymm1 + ymm2 - add packed singles (128 bits). Two sets of 4x floats are added together.
```

## Documentation
Instructions for the SSE unit are documented in volume 4 chapter 2 of the AMD manual. <br>
For introduction and overview over this part of the CPU see volume 1 chapter 4

More on this unit of the processor at later point.
