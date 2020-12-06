# [An Introduction to JVM Bytecode](https://medium.com/swlh/an-introduction-to-jvm-bytecode-5ef3165fae70)

* Can use the `javap` binary to look at `.class` files
  * Adding the `-c` flag "disassembles" the instructions
* Bytecode has no nesteing - each and every instruction is on the same layer and scope
  * The `goto` instruction is the only way to manipulate control flow
* Bytecode is stack-based meaning that bytecode instructions pop previously pushed elements from the stack and push new ones
* `Math.max(1, 2)` might result in the following bytecode

```bash
4: iconst_1
5: iconst_2
6: invokestatic #2
9: pop
```

* `iconst` is used to push `int` literals (in this case `1` and `2`)
* When a method is invoked, the method pops a value for each argument from the stack (starting from the last argument)
  * The method's return value (if it has one) is pushed to the stack
* The `invokestatic` bsaically references which static method to call (in this case, `Math.max`)
* `aload_n` / `iload_n` - like `aload_0` or `iload_2`
  * Pushes local variable's value onto the stack
  * The `a` prefix means it's a reference variable, like an `Object` while `i` refers to the primitive `int`
  * The _value_ of the variable is pushed - so for primitives, it is the "direct" value, but for objects, it is the reference value to the object in the JVM heap
  * Local variable names are assigned an index based on their first occurrence
  * `this` is a local variable, and in every non-static method it is (implicitly) the first argument
  * So `aload_0` in a non-static context always loads a reference to `this` onto the stack

## Array Instructions

* `aaload` - takes an array reference and an index from the stack and pushes whatever is at that index in the array
* `aastore` - takes an array reference, an index, and a value, and assigns the value to the index in the array
* `arraylength` - pushes the length of a given array

## Creating Objects

* The creation of an object and the constructor call are actually separate
* So instantiation like `DivisorPrinter printer = new DivisorPrinter(number);` might lead to bytecode that looks like

```bash
22: new #4 // class DivisorPrinter
25: dup
26: iload_2
28: invokespecial #5 // Method DivisorPrinter."<init>"
30: astore_3
```

* `new #4` creates the object and pushes the reference to the stack
* `invokespecial #5` invokes the constructor
  * Note that constructors are "special" invokeables that are different from `invokevirtual` like instance methods or `invokestatic` like static methods
  * The have a special name ("<init>") and return `void`
* `dup` duplicates the last element on the stack and pushes it onto the stack again, so if there is an object reference on the stack and `dup` is called, there are now two references to that object on the stack
* This is necessary because after the `dup`, there is an `iload_2` call which pushes the value of the `int` variable
* Invoking the constructor consumes two elements from the stack - the explicit `int` argument and the object to call the constructor on
* If the `dup` was not there, nothing would be left on the stack, and wouldn't be able to store the object reference in a variable via `astore_3`
