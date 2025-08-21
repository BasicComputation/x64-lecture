# Floating point numbers
Floating point numbers are numbers with fractions, like 1.25 <br>
Specialized part of CPU operates with floating point numbers. It has it’s own set of registers and instructions.<br>
Operation are for examples + - * / square root, inverse square root and compare.

There are two sizes for floating point numbers: <br>
Single precision floating point (32 bits) and double precision floating point (64 bits) <br>
Or just ‘float’ and ‘double’

###  Encoding
One bit tells whether the number is positive or negative. <br>
In a float the exponent is 8 bits, fractions are 23 bits, fraction is called significant or mantissa.

First bit in mantissa represent when set, ½, next is ¼, and next 1/8, next 1/16… <br>
That is 2^-1, 2^-2, 2^-3 and so on. <br>
The number 1 is added to the mantissa by the CPU, so if mantissa is 0.5 it is actually 1.5 <br>
The exponent bits is a unsigned number, in a float that number is subtracted by the CPU with 127 to get the exponent.

### Example
If resulting exponent is 4, and mantissa is 110100000.... then to calculate the value: <br>
(1 + 2^-1 + 2^-2 + 2^-4) * 2^4 = 2^(0+4) + 2^(-1+4) + 2^(-2+4) + 2^(-4+4)+= 16 + 8 + 4 + 1 = 29

if resulting exponent is -2, and mantissa is 101000.... then to calculate the value: <br>
(1 + 2^-1 + 2^-3) * 2^-2 = 2^(0-2) + 2^(-1-2) + 2^(-3-2) = 0.25 + 0.125 + 0.03125 = 0.40625

Numbers can be very large or small. Cannot represent all possible numbers accuratly because of the fractions used.

### SIMD - Single Instruction Multiple Data
In x64, the unit for floating point operations can hold and perform operations on two sets of 4/8/16 floats or 2/4/8 doubles at once.
There are up to 16 or 32 registers for floating point values. Each of these are up to 256 or 512 bits wide. <br>
When refering to the 128 bit porion the register is xmm, when refering to 256 bit portion its ymm, if 512 bits its zmm. <br> 

Integer operations are also supported on several bytes, word or dwords at the same time. <br>
There are all kinds of special operations, like add with saturation, so when adding bytes the result will not exceed the value 255.

### In assembly
```asm
mov eax, 1000
cvtsi2ss xmm0, eax    ; convert the value in eax to a float, store it in the lower 32 bit of 128 bit register xmm0
                      ; cvt = convert , si = scalar integer , ss = scalar single (float) ... sd = scalar double (double)
                      ; scalar means operate with a single number,
                      ; packed means operate with at least 4 floats or 2 double at the same time.

addss xmm0, xmm1      ; xmm0 = xmm0 + xmm1 - add two floats together
cvtss2si eax, xmm0    ; convert scalar single (float) to scalar integer, rounded to nearest
cvttss2si eax, xmm15  ; convert truncated scalar sigle 2 scalar integer, trunctate the fractions (round down)

addpd xmm2, xmm3      ; add packed double
                      ; 2x double is added with 2x double respectivly
                      ; a0 = a0 + b0 and a1 = a1 + b1
```

### Documentation
Instructions for the SSE unit are documented in volume 4 chapter 2 of the AMD manual. <br>
For introduction and overview over this part of the CPU see volume 1 chapter 4
