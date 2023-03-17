# [Why Doesn't Python Support i++ Increment Syntax](https://towardsdatascience.com/why-doesnt-python-support-i-increment-syntax-ca7f04a2beb)

* Languages such as `C` will treat numerical variable definition differently
  * For something like `int i = 1000`, apply for memory space, bind it to the fixed name `i` and write a variable value of `1000`
  * In Python, `i = 1000` will also apply for memory space, but the number `1000` will be bound (i.e. the address and type of `1000` is immutable)
* In `C`, `i++` means find the value stored at address of `i` and add `1` to it - this new value replaces the old value
  * Does not open new memory spacej
* In Python, `i += 1` means add `1` to the number referenced by `i` and bind the result to a new memory space and stick the name tag `i` on the new number
  * So _if_ it supported `i++` it's more of a creation of a new number since the value (like `1000`) are treated as immutable
* Not supporting `i++` in Python is generally not problematic as there are easy ways to loop in Python that do not need the `i++` incrementing as part of some `for` loop syntax
