# [13.2 Accessing Arguments in a Function](https://bob.cs.sonoma.edu/IntroCompOrg-RPi/sec-arg-access.html)

* The area of the stack used for the activation record is called a stack frame
* A function sets up its own stack frame and stores information like the return address back to the calling function, the calling function's frame pointer, register values that must be saved from the calling function, local variables for the calling function
* In example, main function is passed nine arguments
  * Only four registers available for passing arguments, so function places remaining arguments on the stack
  * Stack pointer points to top of the stack
* Within a sub-function (i.e. the function that the main function calls), the stack pointer / top of the stack is the caller's frame pointer, and the current function's frame pointer is the return address from the calling function
  * The next portion of the stack is the remaining 5 arguments
    * Looking at the assembly language, first four arguments are passed in `r0` to `r3` registers from left-to-right order while any arguments after 4th argument are stored in stack are loaded in reverse order (right to left)
    * So if there are 9 arguments, the 9th argument is loaded first, then 8th, then 7th, etc
  * Within the sub-function, arguments are accessed relative to the frame pointer
    * In example, there are 5 additional int arguments (4 bytes a piece), so each argument has an address that is frame pointer + 4X
    * First argument has an address of frame pointer + 4 bytes, second argument has an address of frame pointer + 8 bytes, etc
  * Within the sub-function, local variables are negative offsets from the frame pointer
  * Frame pointer address should never change, and the calling function's frame pointer is located at a 4 byte offset from the current function's frame pointer
  * When entering calling function, can read function for local variables and allocate space on the stack for them by subtracting the number of bytes required from the stack pointer
  * When leaving sub-function, place return value in `r0` register
  * Deallocate local variables by adding same amount to stack pointer that was subtracted at beginning of function
  * Restore saved register values from the stack for calling function, and return control to the calling function

## Overall layout of stack frame

```bash
| additional     | <-- "end" / "bottom" of stack
 stack memory
| local variables| <-- stack pointer (frame pointer - 8 bytes)
| caller's fp   |
| return address | <-- frame pointer for current function
| 5th argument   | <-- arguments passed in stack frame (frame pointer + 4 bytes)
| 6th argument   |
| 9th argument   | <-- "start" / "top" of stack
```
