# verilog-float-converter

# MacOS: Icarus Verilog

To run the project, install icarus verilog on MacOS with 
`brew install icarus-verilog`

Then compile float.v with
```
iverilog -o float float.v
./float
```

# Description 
A simple routine in Verilog HDL that converts integers into IEEE 754 Floating Point format.

The following approach was used to generate a floating point number: 

We convert the left-hand argument to a positive binary number even if it was stored as negative 2’s compliment. The RHS is passed in as an integer, so we need to convert it to a binary fraction. We do this by finding a power of 10 that is greater than the RHS argument. 

Knowing this value allows us to treat the integer value like a decimal so that we can use the doubling technique to find its binary fractional approximation. 

To get the mantissa value, if there is a value in the left-hand argument, we find the most significant 1-bit and create a bit mask that is the length of the value excluding the most significant 1-bit. Then we apply the AND operation to the mask and the LHS value, and shift the result into the most significant bit for the mantissa value (position 22). 

The RHS fractional value must be combined with the LHS value already stored in the result register. Since we stored our fraction starting in the MSB, we take the value from that register, shift it to the right however many binary digits the left value takes up, and OR the mantissa LHS value with the RHS fraction. 

If the RHS value gets shifted too far to the right and we lose precision, we need to check if there are any 1-bits to the right of where the value gets cut off. If there are, we should round the last bit of the mantissa to 1. This gives us a complete mantissa value for the result. 

The exponent value comes from the length that the most-significant 1-bit is in. This tells us the number of shifts that need to be performed in order to reconstruct the floating-point number from IEEE format. 

The sign bit was set at the beginning of the program when we determined whether to flip the bits of the LHS register from 2’s complement. We can this program by breaking it into functions that allow us to run different values through it. In order to compare results, all of the numbers were checked with an online IEEE 754 float converter https://www.h-schmidt.net/FloatConverter/IEEE754.html.
