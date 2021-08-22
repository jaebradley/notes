# 2.13 Storing Characters

* When translating string statements like `printf("Hello world\n")`  into machine code, the compiler must
  * Store each of the characters in a location in memroy where the control unit can access them
  * Generate the machine instructions to write the characters on the screen
* ASCII uses seven bits to represent each character (use `man ascii`) to generate this table
* The codes from `00-1f` define control characters
  * ASCII was developed in the 60s for transmitting data from a sender to a receiver
  * They were used to "control" the dialog between a sender and a receiver
  * Can be generated on a keyboard by holding the control key down and pressing the related alphabetic key (for example, `ctrl+d` generates an `EOT` character for "End of Transmission")
* ASCII codes are usually stored on the rightmost `7` bits of an eight-bit byte
  * The either bit (the highest-order bit) is called the parity bit
  * It can be used for error detection
  * Sender and receiver agree whether to use even or odd parity
  * Even parity means that an even number of ones are always transmitted with every character and odd parity means that an odd number of ones are transmitted
  * Before transmitting an ASCII character, the sender would adjust the eigth bit such that the total number of ones matched the even or odd agreement
* For the string "Hello World\n", the compiler stores this as a constant array of characters
* In a C-style string, the length of the array of characters is determined by using the sentinel character `NUL` at the end of the string
  * So in the case of "Hello World\n" the compiler must allocate thirteen bytes for the string
  * C uses the `LF` character as a single newline character even though the C syntax requires that the programmer write two characters, "\n"
  * In C, there are three "garbage" bytes after the string
