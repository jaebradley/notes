# [I Finally Understand Static vs. Dynamic Typing and You Will Too!](https://medium.com/hackernoon/i-finally-understand-static-vs-dynamic-typing-and-you-will-too-ad0c2bd0acc7)

* Source code - "original" code typed by human into computer
* At some point there must be translation from source code to something computer can read (i.e. machine code)
* Runtime - program is executing commands
  * Post-compilation if language is compiled
* Compilation - Code is translated before runtime
* Interpreted - Code is translated on the fly, during execution

## When are types checked?

* `"3" + 5` will raise type errors in strongly-typed languages (like Python or Go) because they don't allow for type coercion
* Type coercion is the ability for a value to change types implicitly in certain contexts
* In weakly-typed languages like JavaScript, the result would be `"35"`
* Static typing is when types are checked _before_ runtime
* Dynamic typing is when types are checked on the fly, during execution
* The difference between static and compiled and dynamic and interpreted is that static / dynamic have to do with when types are checked while compiled / interpreted have to do with when source code is translated

```python
# Illustrative Python Example
# Remember that Python is dynamic and interpreted

def foo(a):
    if a > 0:
        print 'Hi'
    else:
        print "3" + 5
foo(2)
```

* `foo(2)` means that the `else` block never executes fo `"3" + 5` is never even evaluated
* Imagine if Python was statically typed - this would mean that a type error would be thrown before code is executed
  * There would still be type-checking _before_ runtime even though it is interpreted
* Now imagine if Python was compiled - the `else` block would be translated _before_ runtime, but because Python is dynamically typed, no error would be thrown
  * This is because, types would still be checked on the fly / during execution
  * Since the `else` is never reached, no type error would throw

## Performance

* Compiled languages that are statically typed will have better runtime performance as knowledge of types allows for machine code optimization
* Statically type languages have better runtime performance because they don't need to check types dynamically while executing
* Compiled languages are faster at runtime as code has already been translated instead of needing to translate on the fly
