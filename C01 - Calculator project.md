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

##
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
when that function returns you must first check for right parenthesis and then check for operator and end of expression as described above.

The start function checks intial condition of an expression:
- If the expression starts with '-' you have to call subtraction function with value 0 as argument
- If the expresion starts with '(' it have to call itself. After function returns check for ')', then the next must be end of expression or an operator.
- If the expression start with a number, you check next part of expression for operator or end of expression. If operator call operator function with number as argument.
- After an operator function returns to start function you must check for operator and end of expression. So this means to go back to check for operator and end of expression.
When end of expression return resulting value.

Along the way you have to check if content of expression is valid, and if the expression follow the rules.

##
### Shunting yard algorithm
Here you create two data structures called stacks, one for numbers one for operators. 
A stack is where you put things on top of the stack in order, and take things off the top in order.
So you need a variable tracking how many things are in the stack. 
Putting things on the stack are called push, while pop is removing from the top, peek is to look at top of stack.

The idea is that when an operator is encountered in the expression, you check if there is one on the top of operator stack, and if so compare them.
If the operator on the stack has greater precedence you pop of the operator and two numbers from number stack, and do calculation with these two numbers according to the operator,
then you push the resulting number on the numbers stack. Then again, compare operator with operator on top of operator stack, if any.
If the one on the stack has lesser precedence, push operator on operator stack, and continue. <br>

So push numbers onto stack, and check operators with top of operator stack, do calculation if operator on stack has greater precedence. <br>

If a left parenthis is encountered, you push it on the operator stack, and continue as if it is a new expression. <br>
When a right parenthesis is encountered, it means that all operations waiting on the stack until left parenthesis must be done. After that pop left parenthesis.

**Procedure** <br>
When you read from the start of an expression, you first have to check for '-', '(', and the procede.
- Initial condition
1. If a minus you push it onto operator stack and the number 0 onto number stack so you have "0 - "
2. If a left parenthesis you push it onto the stack for operators and goto beginning as if a new expression begins.
- Main body
1. Get number, push it onto stack, if not a number its a error.
2. Check for operator, ')', end of expression. <br>
  If operator: Check precedence and do calculation(s), push operator onto stack. Next must be a number or '(', <br>
  If ')': Do calculations until '(' is found, if not found the expression is invalid. <br>
  If end of expression: Do calculations until operator stack is empty. Return result.
   
Along the way you have to check if content of expression is valid, and if the expression follow the rules.

**Example** <br>
Below represent the two stacks and stages
```
expression: 1 + 2 * 3 + 4

                        3
           2      2 *   2 *   6                 4
1   1 +    1 +    1 +   1 +   1 +   7    7 +    7 +    11

Reads:
1    +     2      *     3     +                 4     end of (0)
```




