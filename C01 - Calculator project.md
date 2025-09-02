# Calculator project
Programming is about finding solutions to problems. 

When you have an idea, you need to analyse the idea to get a rough overview of what the different parts of the program must do. 
Then you take each part and further analyse them, breaking it into smaller and smaller pieces. 
It is wise to divide program into small functions. 
Optimization of the program should maby be done when every sub part of a part of the program is fully thought through.

## Text based calculator idea
Create a calculator that takes in mathematical expression from console as text, then output the result to console.

Divide the program into 3 parts: input, calculator, output.
1. Input: <br>
  Takes in arbitrary length expression, so do you need a buffer that can extend or choose max limit on characters?
2. Calculator: <br>
  - Need to be able to convert number in a part of text to floating point value and get the length of number in text.
  - Determine the rules of calculating expression: <br>
    What are the initial possibilites for an expression? What must come after a number or operator? <br>

    In the expression 2*5/3, you need to divide 5 with 3 first, then multiply with 2. <br>
    -2 * (2 + 4 / 3) you need calculate expression within parentheses first, can be considered a expression within expression. <br>
    
    There are several ways of solving this, possible solutions below. <br>    
3. Output: <br>
  To output the result, the floating point value needs to be converted to text and be outputted.

## Methods
The methods I know of:
- Recursive descent parser
- Shunting yard algorithm
- Binary tree

