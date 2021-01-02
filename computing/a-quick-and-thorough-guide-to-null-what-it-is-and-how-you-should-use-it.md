# [A Quick And Thorough Guide To "null": What It Is, And How You Should Use It](https://www.freecodecamp.org/news/a-quick-and-thorough-guide-to-null-what-it-is-and-how-you-should-use-it-d170cea62840/)

* Let's assume 16-bit CPU with 16-bit address space and strings encoded as UTF-16
* So then `String name = Bob` could be store in the following block of memory

```
| A0A1 | B000 | B001 | B002 | B003 |
| B000 | 0042 | 006F | 0062 | 0000 |
```

* The string "Bob" is stored at address `B000` and occupies 4 memory cells
* The variable "name" is stored at address `A0A1` and it's value is the address of the actual string value of "Bob"
  * It holds a "pointer" to "Bob"
* Next instruction reassigns `name` to `null` like `name = null`


```
| A0A1 | B000 | B001 | B002 | B003 |
| 0000 | 0042 | 006F | 0062 | 0000 |
```

* The `name` variable holds a value that now points to `0000`, which is a typical calue used in memory to denote `null`
* Another way to think about `null` is that there is no value associated with `name`
  * Can also think of it as absence of data or simply no data
* Interesting to know that the JVM specification does not mandate a concrete value encoding `null`
* `null` doesn't exist for value types, just reference types
  * A reference is a _pointer_ to a memory-address that stores a value (most likely an _object_ value)
  * A value type just stores the value itself and the concept of `null` does not exist for value types
