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

### Recursive descent parser
Here the problem is split into functions: addition, subtraction, multiplication and division... as well as a start function. <br>

You have a shared pointer (global variable) that holds address of where in the expression its currently in, it is updated for each operator, parenthesis or number.

Each operator function takes in the number to the left of its operator as argument, and returns result of operation.
Each operator function, like addition, will check the next part of expression for operator or end of expression (after a number).
If the next is an operator and has greater presedence (that operation must be done first),
call operator function with the number to the left of that operator as argument, which in turn does the same and eventually returns result.
So then in the case of the addition function add number provided as argument to this function with the return value of function called.
If end of expression you do the operation with number to the left and right of operator and return result.

Each operator function must check for number or left parenthesis after its operator. If it is a left parenthesis you must call the start function, 
when that function returns you must check for operator and end of expression as described above.

The start function checks intial condition of an expression:
- If the expression starts with '-' you have to call subtraction function with value 0 as argument
- If the expresion starts with '(' you have to call itself
- If the expression start with a number, you check next part of expression for operator or end of expression. <br>
  If an operator call operator function with the number as first argument. <br>
  
Then return resulting value
