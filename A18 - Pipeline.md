# Pipeline
Every machine instruction takes some number of steps to execute.

A pipeline is that when a instruction is being executed, the next instruction is fetched from memory at the same time as the execution of the first instruction. 
If possible the CPU will execute a step in the second instruction at the same time as the first is executing a step. 
If the second instruction get to a step that is dependent of the result of the first instruction, the second instruction will have to wait. 
Also if two steps need the same resources, the second instruction will have to wait.

Several instructions will be in the pipeline, this allow up to one instruction being fetched at each clock cycle.
This means the throughput is down to only one clock cycle per instruction, speeding up the execution.

When a conditional jump is encountered, the CPU will try to predict which path the instruction will take, that is which instructions to put in the pipeline, 
if wrong, the instructions in the pipeline from the jump must be discarded.

Recommended presentation: <br>
What Every Programmer Should Know about How CPUs Work • Matt Godbolt • GOTO 2024 <br>
https://www.youtube.com/watch?v=-HNpim5x-IE

Some instructions takes many clock cycles, especially division + modulus (DIV and IDIV). 
So when avoidable the division instruction can be replaced by shift instructions, if division by power of 2, 
or unsigned integer multiply (MUL) with a <br>“magic number” utilizing the upper bits of the result to get the resulting number (along with shifts).
