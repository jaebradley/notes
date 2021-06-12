# [javac](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/javac.html)

* The `javac` tool reads class and interface definitions, written in the Java programming language and compiles them into bytecode class files
* There are two ways to pass source code file names to `javac`
  * For a small number of source files, simply list the file names on the command line
  * For a large number of source files, list the file names in a file, separated by blanks or line breaks
    * Then use the list file name on the `javac` command line, preceded by an `@` character
* Source code file names must have `.java` suffixes, class file names must have `.class` suffixes  * A class called `MyClass` would be written in a source file called `MyClass.java` and compiled into a bytecode class file called `MyClass.class`
* Inner class definitions produce additional class files - these have names combining the inner and outer class names, like `MyClass$MyInnerClass.class`
* By default, the compiler puts each class file in the same directory as its source file

## Searching for Types

* When compiling a source file, the compiler often needs information about a type whose definition did not appear in the source files given on the command line
* The compiler needs type information for every class or interface used, extended, or implemented in the source file
* This includes classes and interfaces not explicitly mentioned in the source file but which provide information through inheritance
* When the compiler needs type information, it looks for a source file or class file which defines the type
* The compiler searches for class files first in the bootstrap and extension classes, then in the user class path (which by default is the current directory)
