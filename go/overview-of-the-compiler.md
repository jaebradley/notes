# [Go: Overview of the Compiler](https://medium.com/a-journey-with-go/go-overview-of-the-compiler-4e5a153ca889)

* Compiler has two categories
  * frontend - phase runs analysis on source code and produces Abstract Syntax Tree
  * backend - phase transforms AST into machine code, implementing varous optimizations

```golang
package main

func main() {
  a := 1
  b := 2
  if true {
    add(a, b)
  }
}

func add(a, b int) {
  println(a + b)
}
```

* First phase of compilation
  * Source code is tokenized (lexical analysis)
  * Source code is parsed (syntax analysis)
  * Syntax tree is constructed for each source file
* AST generation also includes some optimizations like inlining
  * In example, `add` can be inlined since it's only called in one location
* Static Single Assignment
  * Where optimizations occur like dead code elimination, removal of unused branches, replacing expressions with constant values, etc.
  * In example, `a` and `b` can calculate final result and mark variables as unnecessary
  * Similarly, the `true` `if` condition will eliminate the unnecessary block / simplify the control flow
  * Output is assembly code
* Last step is to generate an object file (like `main.o`) that can be used to generate an executable binary
