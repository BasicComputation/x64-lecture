# Calculator project
Programming is about finding solutions to problems. 

When you have an idea, you need to analyse the idea to get a rough overview of what the different parts of the program must do. 
Then you take each part and further analyse them, breaking it into smaller and smaller pieces. 
It is wise to divide program into small functions. 
Optimization of the program should be done when every sub part of a part of the program is fully thought through.


## Text based calculator idea
Create a calculator that takes in mathematical expression from console as text, then output the result to console.

Divide the program into 3 parts: input, calculator, output.
1. Input: <br>
  Takes in arbitrary length expression, so do you need a buffer that can extend or choose max limit on characters?
2. Calculator: <br>
  - Need to be able to convert number in a part of text to floating point value.
  - Text need possibly to be split into parts of numbers, operators (+ - * / ...) and parantheses. <br>
    An array of data structures that can contain information about its type and hold the converted number if a number, makes it manageable.
    Sub problem: how to store a arbitrary length of data? Fixed sized or a extendable buffer.
  - So if you have converted the text into an array of data structures, you need to able to process the data to calculate. <br>
    **Problem:** <br>
    In the expression 2*5/3, you need to divide 5 with 3 first, then multiply the result with 2. <br>
    -2 * (2+4/3) you need calculate expression within parentheses first. <br>
    Possible solutions below.
3. Output: <br>
  To output the result, the floating point value needs to be converted to text and be outputted.

### Calculation methods
The methods I know of:
- Recursive descent parser
- Shunting yard algorithm
- Binary tree

