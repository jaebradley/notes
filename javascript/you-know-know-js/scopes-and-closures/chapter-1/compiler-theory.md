# [Compiler Theory](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch1.md#compiler-theory)

* JavaScript is a compiled language
  * Not compiled well in advance
  * Compilation results are *not* portable among various distributed systems

## Compilation Steps

* Tokenizing/Lexing - breaking up code into meaningful chunks (tokens)
  * `var a = 2;` could be broken up into the tokens `var`, `a`, `=`, `2`, and `;`
* Parsing - takes a stream (array) of tokens and turns it into a tree of nested elements
  * Tree is called an **Abstract Syntax Tree** and represents the grammatical structure of the program
  * Tree for `var a = 2;` could look like
    * Top-level node called `VariableDeclaration`
    * Child node called `Identifier` (whose value is `a`)
    * Another child node called `AssignmentExpression`
      * Which has a child called `NumericLiteral` (whose value is `2`)
* Code-Generation - the process of converting an AST to executable code
  * Basically turn AST for `var a = 2;` into a set of machine instructions to actually create variable `a` (including reserving memory)
